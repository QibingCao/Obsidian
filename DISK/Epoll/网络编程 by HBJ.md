---

类型: 笔记
创建日期: 2024-04-02
修改日期: 2024-04-02
---
>chap01-network.pdf #4.2 套接字
# 套接字
套接字（Socket）是计算机⽹络编程中提供的通信接⼝，⽤于在同⼀计算机或不同计算机的进程之间传输数据；从程序设计的⻆度看，套接字是程序对⽹络协议栈进⾏访问和控制的重要机制。
套接字常⽤的函数原型如下：
```c
#include <sys/so>

int socket(int domain, int type, int protocol);

int bind(int sockfd, struct sockaddr *addr, socklen_t addrlen);

int listen(int sockfd, int backlog);

int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```
## socket()
其中，socket()函数⽤于创建并返回⼀个新的套接字，返回的套接字以⽂件描述符的数据结构给出（即整型 int），这种设计⽅便以⽂件访问的形式，来⼀致的操作套接字。
socket 函数接受三个参数：⽹络域 domain、⽹络类型 type 和协议 protocol；通过给socket 函数提供不同的参数组合，可以定义不同套接字类型，下表给出了常⽤的参数组合。

`int socket(int domain, int type, int protocol);`

| 参数       | 常用取值        | 描述                        |
| -------- | ----------- | ------------------------- |
| domain   | AF_INET     | IPv4协议族                   |
|          | AF_INET6    | IPv6协议族                   |
|          | AF_UNIX     | UNIX域套接字协议族               |
| type     | SOCK_STREAM | 流套接字，用于可靠的、面向连接的传输（TCP）   |
|          | SOCK_DGRAM  | 数据报套接字，用于不可靠的、无连接的传输（UDP） |
|          | SOCK_RAW    | 原始套接字，提供对网络协议的直接访问        |
| protocol | 0           | 自动选择适当的协议                 |
|          | 6           | TCP协议                     |
|          | 17          | UDP协议                     |
在利⽤套接字编程时，经常⽤到以下组合，来创建不同类型的套接字：
```c
/* Create an IPv4 socket for TCP */
socketfd = socket (AF_INET, SOCK_STREAM, 0);

/* Create an IPv4 socket for UDP */
socketfd = socket (AF_INET, SOCK_DGRAM, 0);

/* Create a UNIX domain socket*/
socketfd = socket (AF_UNIX, SOCK_STREAM, 0);

/* Create a raw socket for Ethernet frames */
socketfd = socket (AF_PACKET, SOCK_RAW, htons (ETH_P_ALL));
```
## bind()
函数 bind()将⼀个本地地址与套接字关联，使其可以监听该地址并接受连接请求。
```c
bind(server_sock_fd, (struct sockaddr *)&server_addr,sizeof(server_addr));
```
在上述代码中，我们为 server_sock_fd 设置了监听地址和端⼝号，其中，server_addr是⼀个指向结构体的指针，该结构体包含了希望绑定到的地址和端⼝号。
## listen()
函数 listen()将⼀个套接字设置为监听模式，以便接受来⾃客户端的连接请求。
```c
listen(server_sock_fd, 5)
```
在上⾯的代码中，我们尝试将 server_sock_fd 设置为监听模式，并且指定了等待连接的队列最⼤⻓度为 5。
## accept()
函数 accept()接受来⾃客户端的连接请求，创建并返回⼀个新的套接字，⽤于与客户端通信。
```c
accept(server_sock_fd, (struct sockaddr *)&client_addr,&client_addr_len))
```
