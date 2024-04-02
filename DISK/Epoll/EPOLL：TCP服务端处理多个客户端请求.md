---

类型: 笔记
创建日期: 2024-04-01
修改日期: 2024-04-01
---
# epoll使用实例：TCP服务端处理多个客户端请求

[原文链接huawei](https://bbs.huaweicloud.com/blogs/381801)

上篇文章，介绍了Unix域的socket通信，并通过实例测试了TCP和UDP两种传输方式。本篇，在上篇例程的基础上，来学习epoll的多路复用功能，通过给服务端增加epoll监听功能，实现对多个客户端的数据进行接收。

epoll的全称为eventpoll，是linux内核实现IO多路复用的一个实现。epoll是select和poll的升级版，相较于这两个前辈，epoll改进了工作方式，使之更加高效。本篇暂不介绍epoll的内部实现原理，先来介绍如何使用epoll来实现多路复用功能。

# 1 epoll介绍

## 1.1 epoll创建

```c
int epoll_create(int size); //监听个数
```

## 1.2 epoll事件设置

```c
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)
```

第一个参数epfd是epoll_create()的返回值， 第二个参数op表示动作，用三个宏来表示：

- EPOLL_CTL_ADD：注册新的fd到epfd中；
    
- EPOLL_CTL_MOD：修改已经注册的fd的监听事件；
    
- EPOLL_CTL_DEL：从epfd中删除一个fd；
    

第三个参数是需要监听的fd， 第四个参数是告诉内核需要监听什么事， struct epoll_event结构如下：

```c
struct epoll_event {  
    __uint32_t events; /* Epoll events */
    epoll_data_t data; /* User data variable */
};
```

events可以是以下几个宏的集合：

- EPOLLIN ：表示对应的文件描述符可以读（包括对端SOCKET正常关闭）；
    
- EPOLLOUT：表示对应的文件描述符可以写；
    
- EPOLLPRI：表示对应的文件描述符有紧急的数据可读（这里应该表示有带外数据到来）；
    
- EPOLLERR：表示对应的文件描述符发生错误；
    
- EPOLLHUP：表示对应的文件描述符被挂断；
    
- EPOLLET： 将EPOLL设为边缘触发(Edge Triggered)模式，这是相对于水平触发(Level Triggered)来说的。
    
- EPOLLONESHOT：只监听一次事件，当监听完这次事件之后，如果还需要继续监听这个socket的话，需要再次把这个socket加入到EPOLL队列里
    

## 1.3 epoll监听

```c
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout)
```

等待事件的产生，类似于select()调用。 参数events用来从内核得到事件的集合， maxevents告之内核这个events有多大，这个maxevents的值不能大于创建epoll_create()时的size， 参数timeout是超时时间（毫秒，0会立即返回，-1将不确定，也有说法说是永久阻塞）。 该函数返回需要处理的事件数目，如返回0表示已超时。

# 2 编程实例测试

本次测试在上篇Unix域socket通信代码的基础上进行修改，只使用TCP方式的socket通信进行测试。

上篇的测试代码，服务端接收到一个客户端的连接后，就仅对该客户端进行服务，没有再接收其它客户端的处理逻辑，本篇要实现的，就是一个服务端，能够接收多个客户端的数据。

编程之前，先来看下要实现的程序结构，其中黄色的部分为本篇在上篇例程的基础上，需要增加的部分。

![](https://xxpcb-1259761082.cos.ap-shanghai.myqcloud.com/pic2/linuxcpp/2/1.png)

只需对服务端程序进行修改，添加epoll监听功能，客户端程序不需要修改。

## 2.1 为socket服务端增加epoll监听功能

TCP服务端的代码修改后如下，主要的修改在listen之后，创建一个epoll，然后把服务端的socketfd加入epoll进行监听：

- 当有新的客户端请求连接时，服务端的socketfd会收到事件，进而epoll会收到服务端socketfd的EPOLLIN事件，此时可以让服务端接受客户端的请求，并把创建的客户端fd也加入到epoll进行监听
    
- 当客户端连接成功并被epoll监听后，客户端再发消息过来，epoll就会收到对应客户端fd的EPOLLIN事件，此时可以让服务端读取客户端的消息
    

```c
#define LISTEN_MAX     5
#define EPOLL_FDSIZE   LISTEN_MAX
#define EPOLL_EVENTS   20
#define CLIENT_NUM     3
​
void EpollAddEvent(int epollfd, int fd, int event)
{
    PRINT("epollfd:%d add fd:%d(event:%d)\n", epollfd, fd, event);
    struct epoll_event ev;
    ev.events = event;
    ev.data.fd = fd;
    epoll_ctl(epollfd, EPOLL_CTL_ADD, fd, &ev);
}
​
void TcpServerThread()
{
    //------------socket
    int sockfd = socket(AF_UNIX, SOCK_STREAM, 0);
    if (sockfd < 0)
    {
        PRINT("create socket fail\n");
        return;
    }
    PRINT("create socketfd:%d\n", sockfd);
​
    struct sockaddr_un addr;
    memset (&addr, 0, sizeof(addr));
    addr.sun_family = AF_UNIX;
    strcpy(addr.sun_path, UNIX_TCP_SOCKET_ADDR);
​
    //------------bind
    if (bind(sockfd, (struct sockaddr *)&addr, sizeof(addr)))
    {
        PRINT("bind fail\n");
        return;
    }
    PRINT("bind ok\n");
​
    //------------listen
    if (listen(sockfd, LISTEN_MAX))
    {
        PRINT("listen fail\n");
        return;
    }
    PRINT("listen ok\n");
​
    //------------epoll---------------
    int epollfd = epoll_create(EPOLL_FDSIZE);
    if (epollfd < 0)
    {
        PRINT("epoll create fail\n");
        return;
    }
    PRINT("epoll create fd:%d\n", epollfd);
​
    EpollAddEvent(epollfd, sockfd, EPOLLIN);
​
    struct epoll_event events[EPOLL_EVENTS];
    while(1)
    {
        PRINT("epoll wait...\n");
        int num = epoll_wait(epollfd, events, EPOLL_EVENTS, -1);
        PRINT("epoll wait done, num:%d\n", num);
        for (int i = 0;i < num;i++)
        {
            int fd = events[i].data.fd;
            if (EPOLLIN == events[i].events)
            {
                //接受客户端的连接请求
                if (fd == sockfd)
                {
                    //------------accept
                    int clientfd = accept(sockfd, NULL, NULL);
                    if (clientfd == -1)
                    {
                        PRINT("accpet error\n");
                    }
                    else
                    {
                        PRINT("=====> accept new clientfd:%d\n", clientfd);
                        
                        EpollAddEvent(epollfd, clientfd, EPOLLIN);
                    }
                }
                //读取客户端发来的数据
                else
                {
                    char buf[BUF_SIZE] = {0};
                    //------------recv
                    size_t size = recv(fd, buf, BUF_SIZE, 0);
                    //size = read(clientfd, buf, BUF_SIZE);
                    if (size > 0)
                    {
                        PRINT("recv from clientfd:%d, msg:%s\n", fd, buf);
                    }
                }
            }
        }
    }
​
    PRINT("end\n");
}
```

## 2.2 启动多个客户端进行测试

修改主程序，创建多个客户端线程，产生多个客户端，去连接同一个服务端，来测试epoll监听多个事件的功能。

```javascript
int main()
{
    unlink(UNIX_TCP_SOCKET_ADDR);
​
    //创建一个服务端
    thread thServer(TcpServerThread);
​
    //创建多个客户端
    thread thClinet[CLIENT_NUM];
    for (int i=0; i<CLIENT_NUM; i++)
    {
        thClinet[i] = thread(TcpClientThread);
        sleep(1);
    }
​
    while(1)
    {
        sleep(5);
    }
}
```

本例中，CLIENT_NUM为3，使用3个客户端来测试epoll功能。

## 2.3 测试结果

在Ubuntu上编译运行，程序运行时的打印如下：

```javascript
[TcpServerThread] create socketfd:3
[TcpServerThread] bind ok
[TcpClientThread] create socketfd:4
[TcpServerThread] listen ok
[TcpServerThread] epoll create fd:5
[EpollAddEvent] epollfd:5 add fd:3(event:1)
[TcpServerThread] epoll wait...
[TcpClientThread] create socketfd:6
[TcpClientThread] connect ok
[TcpServerThread] epoll wait done, num:1
[TcpServerThread] =====> accept new clientfd:7
[EpollAddEvent] epollfd:5 add fd:7(event:1)
[TcpServerThread] epoll wait...
[TcpServerThread] epoll wait done, num:1
[TcpServerThread] recv from clientfd:7, msg:helloTCP(fd:4)1
[TcpServerThread] epoll wait...
[TcpClientThread] create socketfd:8
[TcpClientThread] connect ok
[TcpServerThread] epoll wait done, num:2
[TcpServerThread] recv from clientfd:7, msg:helloTCP(fd:4)2
[TcpServerThread] =====> accept new clientfd:9
[EpollAddEvent] epollfd:5 add fd:9(event:1)
[TcpServerThread] epoll wait...
[TcpServerThread] epoll wait done, num:1
[TcpServerThread] recv from clientfd:9, msg:helloTCP(fd:6)3
[TcpServerThread] epoll wait...
[TcpClientThread] connect ok
[TcpServerThread] epoll wait done, num:3
[TcpServerThread] =====> accept new clientfd:10
[EpollAddEvent] epollfd:5 add fd:10(event:1)
[TcpServerThread] recv from clientfd:7, msg:helloTCP(fd:4)5
[TcpServerThread] recv from clientfd:9, msg:helloTCP(fd:6)6
[TcpServerThread] epoll wait...
[TcpServerThread] epoll wait done, num:1
[TcpServerThread] recv from clientfd:10, msg:helloTCP(fd:8)4
[TcpServerThread] epoll wait...
[TcpServerThread] epoll wait done, num:3
[TcpServerThread] recv from clientfd:10, msg:helloTCP(fd:8)7
[TcpServerThread] recv from clientfd:7, msg:helloTCP(fd:4)8
[TcpServerThread] recv from clientfd:9, msg:helloTCP(fd:6)9
[TcpServerThread] epoll wait...
[TcpServerThread] epoll wait done, num:1
[TcpServerThread] recv from clientfd:7, msg:helloTCP(fd:4)10
[TcpServerThread] epoll wait...
[TcpServerThread] epoll wait done, num:2
[TcpServerThread] recv from clientfd:9, msg:helloTCP(fd:6)12
[TcpServerThread] recv from clientfd:10, msg:helloTCP(fd:8)11
[TcpServerThread] epoll wait...
[TcpServerThread] epoll wait done, num:3
[TcpServerThread] recv from clientfd:10, msg:helloTCP(fd:8)14
[TcpServerThread] recv from clientfd:7, msg:helloTCP(fd:4)13
[TcpServerThread] recv from clientfd:9, msg:helloTCP(fd:6)15
[TcpServerThread] epoll wait...
[TcpServerThread] epoll wait done, num:3
[TcpServerThread] recv from clientfd:10, msg:helloTCP(fd:8)16
[TcpServerThread] recv from clientfd:7, msg:helloTCP(fd:4)17
[TcpServerThread] recv from clientfd:9, msg:helloTCP(fd:6)18
[TcpServerThread] epoll wait...
```

对结果标注一下，更容易理解程序运行过程：

![](https://xxpcb-1259761082.cos.ap-shanghai.myqcloud.com/pic2/linuxcpp/2/2.png)

可以看到，服务端依次接受了3个客户端的连接请求，然后可以接收3个客户端发来的数据。

# 3 总结

本篇介绍了linux软件开发中，epoll功能的使用，通过对TCP服务端增加epoll功能，实现一个服务端来处理多个客户端的功能。

