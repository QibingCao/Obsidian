---

类型: 笔记
创建日期: 2024-04-01
修改日期: 2024-04-01
---
## 六、多路复用函数 - epoll()

   `epoll`也是`IO`多路复用模型中最重要的函数，几乎目前绝大部分的高性能框架，都是基于它构建的，例如`Nginx、Redis、Netty`等，所以对于掌握`epoll`知识的必要性显得越发重要。因为在你理解`epoll`之前，你只知道这些技术栈性能很高，但不清楚为什么，而你理解`epoll`之后，对于这些技术栈性能高的原因也就自然就懂了，那接下来我们一起聊聊`epoll`。

   `epoll`与之前的`select、poll`函数不同，它整个过程由`epoll_create、epoll_ctl、epoll_wait`三个函数组成，同时最关键的点也在于：`epoll`直接在内核中维护着一个`FD`集合，外部不再需要将整个要监听的`FD`集合拷贝到内核了，而是调用`epoll_ctl`函数进行管理即可。

> 对于Java-NIO中的`JNI`入口，和之前分析的思路相同，因此就不再进行演示，感兴趣的自己根据执行流程，打开相应的目录文件就可以看到。

接着重点看看`epoll`系列的函数定义。

### 6.1、epoll函数定义

   刚刚聊到过，`epoll`存在三个函数，它们的定义都位于`sys/epoll.h`文件中，那么接下来一个个瞧瞧，先看看`epoll_create`：
```c
int epoll_create(int size); 
int epoll_create1(int flags);
```

你没看错，`create`函数其实有两个，但对于入参为`size`的函数在很早之前就被弃用了，因此一般调用`create`函数都是在调用`epoll_create1()`，这个函数的作用是申请内核创建一个`epollfd`文件，同时申请一个`eventpoll`结构体（稍后讲），然后返回`epollfd`对应的文件描述符。最后再聊聊它的入参：

- `size`：代表指定内核中维护的`FD`集合长度，`2.6.8`版本之后成为了动态集合，**被弃用**。
- `flags`：这个参数主要有两个传递值：
    - `0`：正常创建`epollfd`传入的值。
    - `EPOLL_CLOEXEC`：当`fork`子进程时，子进程不会包含`epoll`的`fd`（多进程`epoll`时使用）。

---

了解了`create`函数后，再来看看`epoll_ctl`函数的定义：
```c
int epoll_ctl(int epfd, int op, int fd, struct epoll_event* event);
```

先来说说`ctl`函数的作用吧，这个函数主要就是对于内核维护的`epollfd`集合进行增删改操作，参数释义如下：
- `epfd`：表示指定要操作的`epollfd`。
- `op`：表示当前要进行的操作，选项如下：
    - `EPOLL_CTL_ADD`：注册操作，代表要往内核维护的集合中新增一个`epollfd`。
    - `EPOLL_CTL_MOD`：修改操作，代表要更改某个`epollfd`所对应的事件。
    - `EPOLL_CTL_DEL`：删除操作，代表要哦承诺内核的集合中移除一个`epollfd`。
- `fd`：表示`epollfd`对应的文件描述符。
- `event`：表示当前描述符的事件队列。

---

最后看看`epoll_wait`函数的定义：
```c
int epoll_wait(int epfd, struct epoll_event* evlist, int maxevents, int timeout);
```
这个函数的作用就类似于之前的`select、poll`，调用之后会阻塞等待至`I/O`事件发生，参数释义如下：

- `epfd`：表示一个等待事件发生的`epollfd`。
- `evlist`：这里用于接收内核已监听到的事件集合。
- `maxevents`：指上述集合出现就绪事件时，一次能够拷贝的最大长度。
    - 如果上述集合中的就绪事件小于该值，则一次性全部拷贝过来。
    - 如果上述集合中的就绪事件大于该值，则一次最多拷贝`maxevents`个事件。
- `timeout`：这个参数和之前的`select、poll`相同，指定超时时间。

> OK，简单了解三个函数后，大家需牢记的一点是：这三个函数都是配套使用的，遵循上述的顺序，以`create、ctl、wait`这种方式依次进行调用，然后就能对一或多个文件描述符进行监听。当然，对于调用后究竟发生了什么？我们接下来通过源码的方式去揭开面纱。

### 6.2、epoll的核心结构体

   在`epoll`中存在两个核心结构体：`epoll_event`、`eventpoll`，这两个结构体贯穿了`epoll`整个流程，这里先简单看看它们的定义：
```c
struct epoll_event 
{ 
	// epoll注册的事件 
	uint32_t events; 
	// 这个可以理解成epoll要监听的FD详细结构体 
	epoll_data_t data; 
} __attribute__ ((__packed__)); 

// 上述data成员的结构定义 
typedef union epoll_data 
{ 
	// 自定义的附带信息，一般传事件的回调函数，当事件发生时， 
	// 通过回调函数将事件添加到list上（Java-Linux-AIO的实现原型） 
	void *ptr; 
	// 要监听的描述符对应 
	int fd; 
	uint32_t u32; 
	uint64_t u64; 
} epoll_data_t;
```

上述的`epoll_event`简单来说，可以将其理解成由“文件描述符-需要监听的事件”组成的键值对结构，其中`data`是文件描述符的详细结构（可以理解成对应着一个FD），而`events`则代表着该`FD`需要监听的事件，这些事件是在调用`epoll_ctl`函数时，由用户态程序指定的，主要有下述一些事件项：

- `EPOLLIN`：表示文件描述符可读。
- `EPOLLOUT`：表示文件描述符可写。
- `EPOLLPRI`：表示文件描述符有[带外数据](https://link.juejin.cn/?target=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%25E5%25B8%25A6%25E5%25A4%2596%25E6%2595%25B0%25E6%258D%25AE%2F10059051 "https://baike.baidu.com/item/%E5%B8%A6%E5%A4%96%E6%95%B0%E6%8D%AE/10059051")可读。
- `EPOLLERR`：表示文件描述符发生错误。
- `EPOLLHUP`：表示文件描述符被挂断。
- `EPOLLET`：将 EPOLL 设为边缘触发(`Edge Trigger`)模式（后续分析）。
- `EPOLLONESHOT`：表示对这个文件描述符只监听一次事件。

简单有个概念之后，再来看看另外一个核心结构体`eventpoll`：
```c
struct eventpoll 
{ 
	// 这个是一把自旋锁（多线程Epoll时使用） 
	spinlock_t lock; 
	// 这个是一把互斥锁（多线程Epoll时使用） 
	// 添加、修改、删除、监听、返回时都会使用这把锁确保线程安全 
	struct mutex mtx; 
	// 调用epoll_wait()时, 会在这个等待队列上**休眠阻塞** 
	wait_queue_head_t wq; 
	// 这个是用于epollfd本身被poll时使用（一般用不上） 
	wait_queue_head_t poll_wait; 
	// 存储所有I/O事件已经就绪的FD链表 
	struct list_head rdllist; 
	// 红黑树结构：存放所有需要监听的节点 
	struct rb_root rbr; 
	// 一个连接着所有树节点的单向链表 
	struct epitem *ovflist; 
	// 这里保存一些用户变量, 如fd监听数量的最大值等 
	struct user_struct *user; 
}; 
struct epitem 
{ 
	// 红黑树节点（red_black_node的缩写） 
	struct rb_node rbn; 
	// 链表节点，方便存储到eventpoll.rdllist中 
	struct list_head rdllink; 
	// 下一个节点指针 
	struct epitem *next; 
	// 当前epitem对应的fd 
	struct epoll_filefd ffd; 
	// 这两个不太懂，似乎跟等待队列有关 
	int nwait; 
	struct list_head pwqlist; 
	// 当前epitem属于哪个eventpoll 
	struct eventpoll *ep; 
	// 链表头 
	struct list_head fllink; 
	// 当前epitem对应的事件（FD需要监听的事件） 
	struct epoll_event event; 
};
```
在前面提到过，`epoll`舍弃了`select、poll`函数中的思想，不再从用户态全量拷贝`FD`集合到内核，而是自己**在内核中**维护了一个`FD`集合，而对于`FD`的管理则是基于`eventpoll`结构实现的，`eventpoll`主要负责管理`epoll`事件，在其内部主要有三个成员需要咱们重点关注：

- `list_head rdllist`：存放所有`I/O`事件已就绪的列表。
- `rb_root rbr`：用于存放注册时`epollfd`描述符的红黑树结构。
- `wait_queue_head_t wq`：休眠阻塞时的等待队列。

当然，对于这个结构体在后续源码中会经常看到，因此稍后会结合源码理解。

### 6.3、Epoll源码深度历险

   整个`Epoll`机制由于是三个函数组成的，因此调试源码时则需要依次调试，我们依旧按照`epoll`的调用顺序对其源码进行剖析。

#### 6.3.1、epoll_create()函数源码分析

`epoll_create()`函数对应的系统调用为`SYSCALL_DEFINE1()`，展开后则对应着内核的`sys_epoll_create`函数，如下：
```c
SYSCALL_DEFINE1(epoll_create, int, size) 
{ 
	if (size <= 0) 
		return -EINVAL; 
	// 直接调用了create1函数 
	return sys_epoll_create1(0); 
}
```
从上述这点即可看出，为何说`size`入参实际上在后续的版本被弃用了，因为无论传入的`size`等于多少，本质上只会判断一下是否小于`0`，然后就调用了`create1()`函数，入参则被写死为`0`了。接着来看看`sys_epoll_create1()`：
```c
SYSCALL_DEFINE1(epoll_create1, int, flags) 
{ 
	int error; 
	struct eventpoll *ep = NULL;//主描述符 
	// 检查一下常量一致性（没啥用） 
	BUILD_BUG_ON(EPOLL_CLOEXEC != O_CLOEXEC); 
	// 判断一下flags是否传递了CLOEXEC 
	if (flags & ~EPOLL_CLOEXEC) 
		return -EINVAL; 
	// 创建一个eventpoll并为其分配空间，分配出错则直接返回执行错误 
	error = ep_alloc(&ep); 
	if (error < 0) 
		return error; 
	// 这里是创建一个匿名真实的FD并与eventpoll关联（稍后细聊） 
	error = anon_inode_getfd("[eventpoll]", &eventpoll_fops, ep, O_RDWR | (flags & O_CLOEXEC)); 
	// 如果前面匿名FD创建失败，释放之前为ep分配的空间 
	if (error < 0) 
		ep_free(ep); 
	// 返回匿名FD或错误码 
	return error; 
}

static int ep_alloc(struct eventpoll **pep) 
{ 
	int error; 
	struct user_struct *user; 
	struct eventpoll *ep; 
	
	// 调用kzalloc为ep分配空间 
	user = get_current_user(); 
	error = -ENOMEM; 
	ep = kzalloc(sizeof(*ep), GFP_KERNEL); 
	if (unlikely(!ep)) goto free_uid; 
	
	// 对ep的每个成员进行初始化 
	spin_lock_init(&ep->lock); 
	mutex_init(&ep->mtx); 
	init_waitqueue_head(&ep->wq); 
	init_waitqueue_head(&ep->poll_wait); 
	INIT_LIST_HEAD(&ep->rdllist); 
	ep->rbr = RB_ROOT; 
	ep->ovflist = EP_UNACTIVE_PTR; 
	ep->user = user; 
	
	*pep = ep; 
	DNPRINTK(3, (KERN_INFO "[%p] eventpoll: ep_alloc() ep=%p\n", current, ep)); 
	return 0; 
	
// 分配空间失败时，清空之前的初始化值，返回错误码 
free_uid: 
	free_uid(user); 
	return error; 
}
```

`sys_epoll_create1()`源码也并不复杂，总共就两步：
- ①调用`ep_alloc()`函数创建并初始化一个`eventpoll`对象。
- ②调用`anon_inode_getfd()`函数把`eventpoll`对象映射到一个`FD`上，并返回这个`FD`。

不过对于第二步，这玩意儿说起来也比较复杂，想深入研究的可以看看 **`Linux`创建匿名FD** 的知识，我们这里就简单的概述一下：

> 由于`epollfd`本身在操作系统上并不存在真正的文件与之对应，所以内核需要为其分配一个真正的`struct file`结构，并且能够具备真正的`FD`，然后前面创建出的`eventpoll`对象则会作为一个私有数据保存在`file.private_data`指针上。这样做的目的在于：为了能够通过`FD`找到一个真实的`struct file`，并且能够通过这个`file`找到`eventpoll`对象，然后再通过`eventpoll`找到`epollfd`，从而能够形成一条“关系链”。

#### 6.3.2、epoll_ctl()函数源码分析

`epoll_create()`函数的源码并不复杂，现在紧接着再来看看管理操作`epoll`的`epoll_ctl()`源码实现，这个函数与之对应的系统调用为`SYSCALL_DEFINE4()`，展开后则对应`sys_epoll_ctl()`，下面一起看看：
```c
SYSCALL_DEFINE4(epoll_ctl, int, epfd, int, op, int, fd, struct epoll_event __user *, event) 
{ 
	int error; 
	struct file *file, *tfile; 
	struct eventpoll *ep; 
	struct epitem *epi; 
	struct epoll_event epds; 
	
	// 错误处理动作及从用户空间将epoll_event结构拷贝到内核空间 
	DNPRINTK(3, (KERN_INFO "[%p] eventpoll: sys_epoll_ctl(%d, %d, %d, %p)\n", current, epfd, op, fd, event)); 
	error = -EFAULT; 
	if (ep_op_has_event(op) && copy_from_user(&epds, event, sizeof(struct epoll_event))) 
		goto error_return; 
		
	// 通过传入的epfd得到前面创建的真实struct file结构 
	error = -EBADF; 
	file = fget(epfd); 
	if (!file) 
		goto error_return; 
	// 这里是获取到需要监听的FD对应的真实struct file结构 
	tfile = fget(fd); 
	if (!tfile) 
		goto error_fput; 
	
	// 判断一下要监听的目标设备是否实现了poll逻辑 
	error = -EPERM; 
	if (!tfile->f_op || !tfile->f_op->poll) 
		goto error_tgt_fput; 
	// 判断一下传递的epfd是否有对应的eventpoll对象 
	error = -EINVAL; 
	if (file == tfile || !is_file_epoll(file)) 
		goto error_tgt_fput; 
	
	// 根据private_data指针获取其中存放的eventpoll对象（上面聊过的） 
	ep = file->private_data; 
	
	// 接下来的操作会开始对内核中的结构进行修改，先加锁确保操作安全 
	mutex_lock(&ep->mtx); 
	// 先从红黑树结构中，根据FD查找一下对应的节点是否存在 
	epi = ep_find(ep, tfile, fd); 
	error = -EINVAL; 
	// 开始判断用户具体要执行何种操作 
	switch (op) { 
		// 如果是要注册（向内核添加一个FD） 
		case EPOLL_CTL_ADD: 
		// 先判断之前是否已经添加过一次当前FD 
			if (!epi) { 
			// 如果没有添加，则调用ep_insert()函数将当前fd注册 
				epds.events |= POLLERR | POLLHUP; error = ep_insert(ep, &epds, tfile, fd); 
			} 
			else 
			// 之前这个FD添加过一次，则返回错误码，不允许重复注册 
				error = -EEXIST; 
				break; 
				
		// 如果是删除（从内核中移除一个FD） 
		case EPOLL_CTL_DEL: 
			// 如果前面从红黑树中能找到与FD对应的节点 
			if (epi) 
				// 调用ep_remove()函数移除相应的节点 
				error = ep_remove(ep, epi); 
			else 
				// 如果红黑树上都没有FD对应的节点，则无法移除，返回错误码 
				error = -ENOENT; 
				break; 
		// 如果是修改（修改内核中FD对应的事件） 
		case EPOLL_CTL_MOD: 
			// 和上面的删除同理，调用ep_modify()修改FD对应的节点信息 
			if (epi) 
			{ 
				epds.events |= POLLERR | POLLHUP; error = ep_modify(ep, epi, &epds); 
			} 
			else
			// 树上没有对应的节点，依旧返回错误码 
				error = -ENOENT; 
				break; 
			} 
	// 修改完成之后，为了确保其他进程可操作，记得释放锁哦~ 
	mutex_unlock(&ep->mtx); 
// 这里是对应上述各种错误情况的goto 
error_tgt_fput: 
	fput(tfile); 
error_fput: 
	fput(file); 
error_return: 
	DNPRINTK(3, (KERN_INFO "[%p] eventpoll: sys_epoll_ctl(%d, %d, %d, %p) = %d\n", current, epfd, op, fd, event, error)); 
	return error; 
}
```

`epoll_ctl()`函数的实现过程，看起来是相当直观明了，总结一下：

- ①先将用户传递的`epoll`事件集合`epoll_event`结构从用户空间拷贝到内核。
- ②通过`epfd`找到与之对应的`struct file`结构，再找到`FD`对应的`file`结构。
- ③判断要监听的`FD`设备是否实现了`poll`功能，再根据`private_data`获取`eventpoll`。
- ④上锁，然后通过传入的`fd`在红黑树中查找有没有对应的节点，然后处理用户操作。
- ⑤如果是注册操作，先判断当前`FD`之前是否注册过，在树上是否有相应节点：
    - 有：代表之前已经添加过一次，不能重复添加，返回错误码。
    - 没有：调用`ep_insert()`函数，将当前`FD`添加到红黑树中。
- ⑥如果是删除操作，先看一下树上有没有与目标`FD`对应的节点：
    - 有：调用`ep_remove()`函数，将当前`FD`对应的节点从树上移除。
    - 没有：代表`FD`之前都没有添加过，找不到要移除的节点，返回错误码。
- ⑦如果是修改操作，先看一下树上有没有与目标`FD`对应的节点：
    - 有：调用`ep_modify()`函数，根据用户的操作项，修改对应节点信息。
    - 没有：代表`FD`之前都没有添加过，找不到要修改的节点，返回错误码。
- ⑧操作完成后，释放锁，同时如果前面有错误则利用`goto`处理前面的错误信息。

相信认真看一遍源码，以及上述流程后，对于`epoll_ctl()`函数的逻辑就明白了，当然，诸位有些绕的地方估计在`epoll`内部结构之间的关系，上个图理解：  
![epoll结构](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e91fa784ecdf407eb4baa2859dd1c2a9~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)  
整个结构关联起来略显复杂，但如若之前的`epoll_create()`函数真正理解后，其实也并不难懂，调用`epoll_create`后会先创建两个结构体：一个`file`结构、一个`eventpoll`结构，然后会将`eventpoll`保存在`file.private_data`指针中，同时再将这个`file`的文件描述符返回给调用者（用户态程序），此时这个返回的`FD`就是所谓的`epollfd`。

然后当我们调用`epoll_ctl()`尝试将一个要监听的`SocketFD`加入到内核时，我们首先需要传递一个`epfd`，而后再`ctl()`函数内部会根据这个`epfd`找到之前创建的`file`结构，再根据其`private_data`指针找到前面创建的`eventpoll`对象，然后定位该对象的内部成员：`rbr`红黑树，在将要监听的`FD`封装成`epitem`节点加入树中。

> 当然，为了求证上述观点，接下来再看看`ep_insert()`函数的实现。
```c
static int ep_insert(struct eventpoll *ep, struct epoll_event *event, struct file *tfile, int fd) 
{ 
	int error, revents, pwake = 0; 
	unsigned long flags; 
	struct epitem *epi; 
	// 这里是一个新的结构体（类似于select、poll中的poll_wqueues结构） 
	struct ep_pqueue epq; 
	
	// 检查目前是否达到了当前用户进程的最大监听数 
	if (unlikely(atomic_read(&ep->user->epoll_watches) >= max_user_watches)) 
		return -ENOSPC; 
	// 利用SLAB机制分配一个epitem节点 
	if (!(epi = kmem_***_alloc(epi_***, GFP_KERNEL))) 
		return -ENOMEM; 
	// 初始化epitem节点的一些成员 
	INIT_LIST_HEAD(&epi->rdllink); 
	INIT_LIST_HEAD(&epi->fllink); 
	INIT_LIST_HEAD(&epi->pwqlist); 
	epi->ep = ep; 
	// 将要监听的FD以及它的file结构设置到epitem.ffd成员中 
	ep_set_ffd(&epi->ffd, tfile, fd); 
	epi->event = *event; 
	epi->nwait = 0; 
	epi->next = EP_UNACTIVE_PTR; 
	// 同时开始准备调用fd对应设备的poll 
	epq.epi = epi; 
	
	// 这里和select、poll差不多，设置执行poll_wait()时， 
	// 其回调函数为ep_ptable_queue_proc(稍后分析) 
	init_poll_funcptr(&epq.pt, ep_ptable_queue_proc); 
	// tfile是需要监听的fd对应的file结构 
	// 这里就是去调用fd对应设备的poll，询问I/O数据是否可读写 
	revents = tfile->f_op->poll(tfile, &epq.pt); 
	// 这里是防止执行出现错误的检测动作 
	error = -ENOMEM; 
	if (epi->nwait < 0) 
		goto error_unregister; 
	// 每个FD会将所有监听自己的epitem链起来 
	spin_lock(&tfile->f_lock); 
	list_add_tail(&epi->fllink, &tfile->f_ep_links); 
	spin_unlock(&tfile->f_lock); 
	
	// 上述所有工作完成后，将epitem插入到红黑树中 
	ep_rbtree_insert(ep, epi); 
	
	// 判断一下前面调用poll之后，对应设备上的I/O事件是否就绪 
	if ((revents & event->events) && !ep_is_linked(&epi->rdllink)) 
	{ 
		// 如果已经就绪，那直接将当前epitem节点添加到eventpoll.rdllist中 
		list_add_tail(&epi->rdllink, &ep->rdllist); 
		// 同时唤醒正在阻塞等待的进程 
		if (waitqueue_active(&ep->wq)) 
			wake_up_locked(&ep->wq); 
		if (waitqueue_active(&ep->poll_wait)) 
			pwake++; 
	} 
	spin_unlock_irqrestore(&ep->lock, flags); 
	atomic_inc(&ep->user->epoll_watches); 
	// 调用poll_wait()执行回调函数（为了防止锁资源占用，这是在锁外调用） 
	if (pwake) ep_poll_safewake(&ep->poll_wait); 
		return 0; 
// 执行出错的goto代码块 
error_unregister: 
	ep_unregister_pollwait(ep, epi); 
	spin_lock_irqsave(&ep->lock, flags); 
	if (ep_is_linked(&epi->rdllink)) 
	list_del_init(&epi->rdllink); 
	spin_unlock_irqrestore(&ep->lock, flags); 
	kmem_***_free(epi_***, epi); 
	return error; 
}
```


其实对于`ep_insert()`这个函数呢，说清楚来也并不复杂，简单总结一下：

- ①对于要监听的`FD`会先分配一个`epitem`节点，并且根据`FD`对节点进行初始化。
- ②最开始声明了一个结构体`ep_pqueue`，然后会利用它为`FD`设置`poll_wait`回调函数。
- ③尝试调用`FD`对应设备的`poll`，询问当前`FD`的`I/O`数据是否可被读写。
- ④调用`ep_rbtree_insert()`函数将已经构建好的`epitem`节点插入红黑树中。
- ⑤判断一下当前`FD`的`I/O`事件是否已就绪（可被读写），如果可以则唤醒等待的进程。

当然，对于新的结构体`ep_pqueue`，它的功能和之前聊到的`poll_wqueues`功能大致相同，主要用于设置唤醒回调，定义如下：
```c
struct ep_pqueue 
{ 
	poll_table pt; 
	struct epitem *epi; 
};
```
很明显就可以看出，与`poll_wqueues`结构中同样存在`poll_table`成员，不熟悉的跳回之前讲`poll_wqueues`的环节。不过`Epoll`与`Select、Poll`还是存在些许不同的，在之前设置`poll_wait()`的回调函数是`__pollwait()`，但在这里设置的确是`ep_ptable_queue_proc()`函数，那这个函数会做什么事情呢？来看看：
```c
static void ep_ptable_queue_proc(struct file *file, wait_queue_head_t *whead, poll_table *pt) 
{ 
	struct epitem *epi = ep_item_from_epqueue(pt); 
	struct eppoll_entry *pwq; 
	if (epi->nwait >= 0 && (pwq = kmem_***_alloc(pwq_***, GFP_KERNEL))) { 
		// 初始化等待队列, 指定ep_poll_callback为唤醒时的回调函数 
		init_waitqueue_func_entry(&pwq->wait, ep_poll_callback); 
		pwq->whead = whead; pwq->base = epi; 
		// 将节点加入到等待队列中..... 
		add_wait_queue(whead, &pwq->wait); 
		list_add_tail(&pwq->llink, &epi->pwqlist); 
		epi->nwait++; 
	} 
	else 
	{ 
		/* We have to signal that an error occurred */ 
		epi->nwait = -1; 
	} 
}
```

上述重心我们只需要知道一个点，这个函数会在执行`f_op->poll`时被调用的，在这里最重要的是设置了一个唤醒时的回调函数`ep_poll_callback()`，也就是当某个设备上`I/O`事件就绪后，唤醒进程时会调用的函数，实现如下：
```c
static int ep_poll_callback(wait_queue_t *wait, unsigned mode, int sync, void *key) 
{ 
	int pwake = 0; 
	unsigned long flags; 
	// 从等待队列获取epitem节点（主要目的在于要确认哪个进程在等待当前设备就绪） 
	struct epitem *epi = ep_item_from_wait(wait); 
	// 获取当前epitem节点所在的eventpoll 
	struct eventpoll *ep = epi->ep; 
	spin_lock_irqsave(&ep->lock, flags); 
	if (!(epi->event.events & ~EP_PRIVATE_BITS)) 
		// 检测错误 goto out_unlock; 
		// 检测目前设备上就绪的事件是否为我们要监听的事件 
		if (key && !((unsigned long) key & epi->event.events)) 
			// 如果不是，则直接跳转goto goto out_unlock; 
			// 这里是用来处理事件并发出现时的情况， 
			// 假设当前的回调方法被执行，但epoll_wait()已经获取到了别的IO事件， 
			// 那么此时将当前设备发生的事件，epitem会用一个链表存储， 
			// 此时不立即发给应用程序，也不丢弃本次IO事件， 
			// 而是等待下次调用epoll_wait()函数时返回 
			if (unlikely(ep->ovflist != EP_UNACTIVE_PTR)) 
			{ 
				if (epi->next == EP_UNACTIVE_PTR) 
				{ 
					epi->next = ep->ovflist; 
					ep->ovflist = epi; 
				} 
				// 然后直接跳转goto 
				goto out_unlock; 
			} 
			// 正常情况下，将当前已经触发IO事件的epitem节点放入readylist就绪列表 
			if (!ep_is_linked(&epi->rdllink)) 
				list_add_tail(&epi->rdllink, &ep->rdllist); 
				// 唤醒调用epoll_wait后阻塞的进程... 
				if (waitqueue_active(&ep->wq)) 
					wake_up_locked(&ep->wq); 
				// 如果epollfd也在被poll, 也唤醒队列里面的所有成员(多进程epoll情况) 
				if (waitqueue_active(&ep->poll_wait)) 
					pwake++; 
// 前面的goto跳转处理 
out_unlock: 
	spin_unlock_irqrestore(&ep->lock, flags); 
	if (pwake) ep_poll_safewake(&ep->poll_wait); 
	return 1; 
}
```

这个唤醒回调函数，主要干的事情就是处理了几种特殊情况，然后将`IO`事件就绪的节点添加到了`eventpoll.readylist`就绪列表，紧接着唤醒了调用`epoll_wait()`函数后阻塞的进程。

> 至此，`epoll_ctl()`函数调用后，会执行的流程就已经分析明白了。当然，对于具体如何插入、移除、修改节点的函数就不分析了，这里就是红黑树结构的知识，大家可参考`HashMap`集合的元素管理原理。接下来重点看看`epoll_wait()`函数。

#### 6.3.3、epoll_wait()函数源码分析

`epoll_wait()`也是整个`Epoll`机制中最重要的一步，前面的`create、ctl`函数都仅仅是在为`wait`函数做准备工作，`epoll_wait()`是一个阻塞函数，调用后会导致当前程序发生阻塞等待，直至获取到有效的`IO`事件或超时为止。

`epoll_wait()`对应的系统调用为`SYSCALL_DEFINE4()`函数，展开后则是`sys_epoll_wait()`，不多说直接上源码：
```c
SYSCALL_DEFINE4(epoll_wait, int, epfd, struct epoll_event __user *, events, int, maxevents, int, timeout) 
{ 
	int error; 
	struct file *file; 
	struct eventpoll *ep; 
	// 获取的最大事件数量必须大于0，并且不超出ep的最大事件数 
	if (maxevents <= 0 || maxevents > EP_MAX_EVENTS) 
		return -EINVAL; 
	// 内核会验证用户接收事件的这一段内存空间是不是有效的. 
	if (!access_ok(VERIFY_WRITE, events, maxevents * sizeof(struct epoll_event))) 
	{ 
		error = -EFAULT; 
		goto error_return; 
	} 
	error = -EBADF; 
	// 根据epollfd获取对应的struct file真实文件 
	file = fget(epfd); 
	if (!file) 
		goto error_return; 
	error = -EINVAL; 
	// 检查一下获取到的file是不是一个epollfd 
	if (!is_file_epoll(file)) 
		goto error_fput; 
	// 获取file的private_data数据，也就是根据file获取eventpoll对象 
	ep = file->private_data; 
	// 获取到eventpoll对象调用ep_poll()函数（这个是核心函数！） 
	error = ep_poll(ep, events, maxevents, timeout); 
	
error_fput: 
	fput(file); 
error_return: 
	return error; 
}
```

上述逻辑也不难，首先对于用户态调用`epoll_wait()`函数时传递的一些参数进行了效验，因为内核对于进程采取的态度是绝对不信任，因此对于用户进程递交的任何参数都会进行效验，确保无误后才会采取下一步措施。当上述代码前面效验了参数的“合法性”后，又根据`epfd`获取了对应的`file`，然后又根据`file`获取到了`eventpoll`对象，最后调用了`ep_poll()`函数并传入了`eventpoll`对象，再看看这个函数：
```c
static int ep_poll(struct eventpoll *ep, struct epoll_event __user *events, int maxevents, long timeout) 
{ 
	int res, eavail; 
	unsigned long flags; 
	long jtimeout; 
	// 等待队列 
	wait_queue_t wait; 
	// 如果调用epoll_wait时传递了阻塞时间，那么先计算休眠时间, 
	// 毫秒要转换为HZ电磁波动的频率（比较严谨，控制的非常精细） 
	jtimeout = (timeout < 0 || timeout >= EP_MAX_MSTIMEO) ?
		 MAX_SCHEDULE_TIMEOUT : (timeout * HZ + 999) / 1000; 
retry: 
	spin_lock_irqsave(&ep->lock, flags); 
	res = 0; 
	// 判断一下eventpoll.readylist事件就绪列表是否为空 
	if (list_empty(&ep->rdllist)) 
	{ 
		// 初始化等待队列，准备将当前进程挂起阻塞 
		init_waitqueue_entry(&wait, current); 
		// 挂载到如果eventpoll.wq等待队列
		__add_wait_queue_exclusive(&ep->wq, &wait); 
		
		// 核心循环！ 
		for (;;) 
		{ 
			// 准备进入阻塞，先将当前进程设置为睡眠状态(可被信号唤醒) 
			set_current_state(TASK_INTERRUPTIBLE); 
			// 如果睡眠之前，readylist中有数据了或已经到了给定的超时事件 
			if (!list_empty(&ep->rdllist) || !jtimeout) 
				break; 
			// 不睡了，直接中断循环 
			// 如果出现唤醒信号，也中断循环，不睡了退出干活 
			if (signal_pending(current)) 
			{ 
				res = -EINTR; 
				break; 
			} 
			// 上述的几个情况都未发生，在这里准备正式进入睡眠状态 
			spin_unlock_irqrestore(&ep->lock, flags); 
			// 开始睡觉(如果指定了阻塞时间，jtimeout时间过后会自动醒来) 
			jtimeout = schedule_timeout(jtimeout); // 正式进入睡眠阻塞 
			spin_lock_irqsave(&ep->lock, flags); 
		} 
		// 出现唤醒信号、或事件已就绪、或已超时，则将进程从等待队列移除 
		__remove_wait_queue(&ep->wq, &wait); 
		// 这里设置一下当前进程的状态为运行状态（活跃状态） 
		set_current_state(TASK_RUNNING); 
	} 
	// 因为超时的情况下当前进程也会醒来，所以这里需要再次判断一下： 
	// 目前到底是否已经有数据就绪了，这里会返回一个布尔值给 eavail 
	eavail = !list_empty(&ep->rdllist) || ep->ovflist != EP_UNACTIVE_PTR; 
	spin_unlock_irqrestore(&ep->lock, flags); 
	// 如果确定有事件发生，那则调用ep_send_events()将事件拷贝给用户进程 
	if (!res && eavail && !(res = ep_send_events(ep, events, maxevents)) && jtimeout) 
		goto retry; 
	// 最后返回本次监听到的事件数 
	return res; 
}
```


上述的`ep_poll()`函数，对比`select、poll`而言要简单很多，整个流程也没几步：

- ①转换给定的阻塞时间，并判断就绪列表中事件是否已就绪，没有则准备休眠阻塞。
- ②先初始化等待队列并挂载到`eventpoll.wq`中，然后进入循环，设置进程的状态。
- ③在正式进入睡眠之前，再次检测是否有事件就绪、是否已超时、是否出现唤醒信号。
- ④如果都没有出现，则调用`schedule_timeout(jtimeout)`函数让进程睡眠一定时间。
- ⑤当睡眠超时、或出现唤醒信号、或事件已就绪，将当前进程从队列移除并设置运行状态。
- ⑥有事件到来非超时的情况下，则调用`ep_send_events()`将就绪事件拷贝给用户进程。

OK~，上述总结的流程已经描述的十分清晰了，接下来再看看拷贝就绪事件的函数`ep_send_events()`：
```c
static int ep_send_events(struct eventpoll *ep, struct epoll_event __user *events, int maxevents) 
{ 
	struct ep_send_events_data esed; 
	// 获取用户进程传递的maxevents值 
	esed.maxevents = maxevents; 
	// 获取用户态存放就绪事件的集合 
	esed.events = events; 
	// 调用ep_scan_ready_list()函数进行具体处理 
	return ep_scan_ready_list(ep, ep_send_events_proc, &esed); 
}
```

这个函数非常简单，一眼看明白了，其实最终的拷贝工作是由`ep_scan_ready_list()`完成的，那么再来看看它：
```c
static int ep_scan_ready_list(struct eventpoll *ep, int (*sproc)(struct eventpoll *, struct list_head *, void *), void *priv) 
{ 
	int error, pwake = 0; 
	unsigned long flags; 
	struct epitem *epi, *nepi; 
	LIST_HEAD(txlist); 
	// 先上锁，防止出现安全问题 
	mutex_lock(&ep->mtx); 
	spin_lock_irqsave(&ep->lock, flags); 
	// 将rdllist中所有就绪的节点转移到txlist，然后清空rdllist 
	list_splice_init(&ep->rdllist, &txlist); 
	ep->ovflist = NULL; 
	spin_unlock_irqrestore(&ep->lock, flags); 
	// sproc是前面调用当前函数时传递的ep_send_events_proc()， 
	// 会通过这个函数处理每个epitem节点 
	error = (*sproc)(ep, &txlist, priv); spin_lock_irqsave(&ep->lock, flags); 
	// 之前曾讲到过，如果epoll正在拷贝数据时又发生了IO事件， 
	// 那么则会将这些IO事件保存在ovflist组成一个链表，现在来处理这些事件 
	for (nepi = ep->ovflist; (epi = nepi) != NULL; nepi = epi->next, epi->next = EP_UNACTIVE_PTR) 
	{ 
		// 将这些直接放入readylist列表中 
		if (!ep_is_linked(&epi->rdllink)) 
		list_add_tail(&epi->rdllink, &ep->rdllist); 
	} 
	ep->ovflist = EP_UNACTIVE_PTR; 
	// 上一次没有处理完的epitem节点, 重新插入到readylist 
	// 因为epoll一次只能拷贝maxevents个事件返回用户态 
	list_splice(&txlist, &ep->rdllist); 
	// readylist不为空, 直接唤醒 
	if (!list_empty(&ep->rdllist)) 
	{ 
		// 唤醒的前置工作 
		if (waitqueue_active(&ep->wq)) 
			wake_up_locked(&ep->wq); 
		if (waitqueue_active(&ep->poll_wait)) 
			pwake++; 
	} 
	spin_unlock_irqrestore(&ep->lock, flags); 
	mutex_unlock(&ep->mtx); 
	// 为了防止长时间占用锁，在锁外执行唤醒工作 
	if (pwake) 
		ep_poll_safewake(&ep->poll_wait); 
	return error; 
}
```

流程就不写了，源码中标注的很清楚，下面再来看看`ep_send_events_proc()`函数是如何处理每个`epitem`节点的：
```c
// 注意点：这里的入参list_head并不是readylist，而是上面函数的txlist 
static int ep_send_events_proc(struct eventpoll *ep, struct list_head *head, void *priv) 
{ 
	struct ep_send_events_data *esed = priv; 
	int eventcnt; 
	unsigned int revents; 
	struct epitem *epi; 
	struct epoll_event __user *uevent; 
	// 先用循环扫描整个列表（不一定会全部处理，最多只处理maxevents个） 
	for (eventcnt = 0, uevent = esed->events; !list_empty(head) && eventcnt < esed->maxevents;) 
	{ 
		// 依次获取到其中的一个epitem节点 
		epi = list_first_entry(head, struct epitem, rdllink); 
		// 紧接着从列表中将这个节点移除 
		list_del_init(&epi->rdllink); 
		// 再次读取当前节点对应FD所触发的事件，其实在唤醒回调函数中， 
		// 这个工作也执行过一次，那为啥这里还需要做一次呢？ 
		// 答案是：为了确保读到最新的事件，因为有些FD可能前面触发了读就 
		// 绪事件，后面又触发了写就绪事件，因此这里要确保严谨性。 
		revents = epi->ffd.file->f_op->poll(epi->ffd.file, NULL) & epi->event.events; 
		if (revents)
		{ 
			// 调用__put_user将就绪的事件拷贝至用户进程传递的事件集合中 
			if (__put_user(revents, &uevent->events) || __put_user(epi->event.data, &uevent->data)) 
			{ 
				list_add(&epi->rdllink, head); 
				return eventcnt ? eventcnt : -EFAULT; 
			} 
			eventcnt++; 
			uevent++; 
			if (epi->event.events & EPOLLONESHOT) 
				epi->event.events &= EP_PRIVATE_BITS; 
			else if (!(epi->event.events & EPOLLET)) 
			{ 
				// 大名鼎鼎的ET和LT，就在这一步会有不同（稍后分析） 
				list_add_tail(&epi->rdllink, &ep->rdllist); 
			} 
		} 
	} 
	return eventcnt; 
}
```

处理每个`epitem`节点的函数中，重点就做了两件事：

- ①读取了每个`epitem`节点最新的就绪事件。
- ②调用`__put_user()`函数将就绪的事件拷贝至用户进程传递的`evlist`集合中。

至此，`epoll_wait()`函数的核心源码也全都走了一遍，最后来简单的总结一下。

#### 6.3.4、Epoll被阻塞的进程是如何唤醒的？

   到这里，大家应该有个疑惑：`epoll_wait()`函数中，本质上只做了进程休眠阻塞的工作，那它什么时候会被唤醒呢？先对于这个点回答一下：大家还记得前面分析`epoll_ctl()`函数时，在其中调用了每个`FD`的`poll`吗？

> `revents = tfile->f_op->poll(tfile, &epq.pt);`

也就是这行代码，在调用`epoll_ctl()`函数向内核插入一个节点时，就会先询问一次`FD`的`IO`数据是否可被读写，此时如果可以就会直接将这个节点添加到`readylist`列表中，但如果对应驱动设备的`IO`事件还未就绪，则会将当前进程注册到每个`FD`对应设备的等待队列上，并设置唤醒回调函数为`ep_poll_callback()`。

这样也就意味着，如果某个`FD`的事件就绪了，就会由对应的驱动设备执行这个回调，在`ep_poll_callback()`函数中，会先将对应的节点先插入到`readylist`列表，然后会尝试唤醒`eventpoll`等待队列中阻塞的进程。

当后续调用`epoll_wait()`函数时，会先判断`readylist`列表中是否有事件就绪，如果有就直接读取返回了，如果没有则会让当前进程阻塞休眠，并将当前进程添加到`eventpoll`等待队列中，然后某个`FD`的数据就绪后，则会唤醒这个队列中阻塞的进程，此时调用`epoll_wait()`陷入阻塞的进程就被唤醒工作了！！发现没有，`Epoll`的源码设计中是环环相扣的，十分巧妙！

> OK~，搞清楚这点之后，`Epoll`的核心逻辑已经讲完了十之八九，还剩下`Epoll`的两种事件触发机制未讲到，来聊一聊吧。

### 6.4、Epoll的两种事件触发机制

   相信之前有简单了解过`Epoll`的小伙伴都明白，在`Epoll`中有两种事件触发模式，分别被称为水平触发与边缘触发，一般来说，边缘触发的性能远超于水平触发。

#### 6.4.1、Epoll水平触发机制-LT模式（Level Trigger）

   `LT`模式也是`Epoll`的默认事件触发机制，也就是当某个`FD`（`epitem`节点）被处理后，如果还依旧存在事件或数据，则会再次将这个`epitem`节点加入`readylist`列表中，当下次调用`epoll_wait()`时依旧会返回给用户进程。

> 大家还记得前面分析`ep_send_events_proc()`函数时，最后的那两行代码吗？
```c
else if (!(epi->event.events & EPOLLET)) 
{ 
	list_add_tail(&epi->rdllink, &ep->rdllist); 
}
```

在这个函数中，最后面做了一个简单的判断，如果当前`Epoll`的工作模式没有设置成`EL`，同时当前节点还有事件未处理，就会调用`list_add_tail()`函数将当前的`epitem`节点重新加入`readylist`列表。反之，`ET`模式下则不会这么做。

#### 6.4.2、Epoll边缘触发机制-ET模式

   在`ET`模式中，就是和`LT`模式反过来的，当处理一个`epitem`节点时，就算其中还有事件没处理完，那我也不会将这个节点重新加入`readylist`列表，除非这个节点对应的`FD`又再次触发了新事件，然后再次执行了`ep_poll_callback()`回调函数，此时才会将其重新加入到`readylist`。

> 说人话简单一点：就是`ET`模式下，对于当前触发的事件，只会通知用户进程一次，就算没有处理也不会重复通知，除非这个`FD`发生新的事件。而`LT`模式下则相反，无论何种情况下都能确保事件不丢失。

那又该如何设置`ET`触发机制呢？**其实也就是在调用`epoll_ctl()`函数时**，指定感兴趣的监听事件时，多加一个`EPOLLET`即可。

> 基于`Epoll`机制构建的大部分高性能应用，一般都会采用`ET`模式，例如`Nginx`。

### 6.5、Epoll小结与一个争议的问题

   相较于之前的`select`函数存在的四个问题，在`Epoll`中得到了合理解决，但也并非`Epoll`的性能就一定比`Select、Poll`要好，在监听的文件描述符较少、且经常更换监听的目标`FD`的情况下，`Select、Poll`的性能反而会更佳。

> 当然，`epoll`在高并发的性能下，会有非常优异的表现，这是由于多方面原因造就的，比如在内核中维护`FD`避免反复拷贝切换、对于就绪事件回调通知，无需用户进程再次轮询查找、内部采用红黑树结构维护节点、退出`ET`事件触发机制等.......

   同时对于网上一个较为争议的问题：**`Epoll`到底有没有试用`MMAP`共享内存呢**？从`Epoll`源码的角度来看，其实是并未使用的，在向用户进程返回就绪事件时，本质上是调用了`__put_user()`函数将数据从内核拷贝到了用户态。当然，我`Epoll`的源码是基于内核`3.x`版本的，但听网上说在早版本里面用到了，但翻阅分析`select`源码时用的内核`2.6`版本源码，在里面`Epoll`都还未定义完整，仅有部分实现，所以也没有发现`mmap`相关的`API`调用。

> 不过在`Java-NIO`的`FileChannel.transferTo()`方法中，以及在`Linux`系统的`sendfile()`函数中确实用到了，因此操作本地文件数据时，确实会用到`mmap`共享内存。因此，`Java-NIO`中用到了`mmap`，但`Epoll`中应该未曾用到。

## 七、多路复用模型总结

   看到到这里，也就接近尾声了，上面已经对于`Linux`系统下提供的多路复用函数进行了全面深入剖析，大家反复阅读几遍，自然能够彻底弄懂`select、poll、epoll`这些函数的工作原理。

> 不过对于`Epoll`的分析还有一些内容未曾提及，也就是`Epoll`唤醒时的[[epoll 惊群问题]]，大家感兴趣的可自行去研究，这里只埋下一个引子，当然也并不复杂。

   而整个`Java-NIO`都是基于底层的多路复用函数构建的，但本篇仅分析了`Linux`系统下的多路复用实现，在本文开篇也提到过：`JVM`为了维持自己的跨平台性，因此在不同的系统下会分别调用不同的多路复用函数，比如`Windows-Select、Mac-KQueue`函数，其中`Windows-Select`与`Linux-Select`的实现类似，因此我从`JNI`调用的入参上来说大致相同。而`Mac-KQueue`则与`Linux-Epoll`类似。但对于其内部实现我并不清楚，毕竟是闭源的系统，大家有兴趣可以自行研究。

> 最后，还有`Java-AIO`的内容，其底层是如何实现的呢？在异步非阻塞式`IO`的支持方面，`Windows`系统反而做的更好，因为它有专门实现`IOCP`机制，但`Linux、Mac`系统则是通过`KQueue、Epoll`模拟实现的。

至此，最后也简单的总结一下本篇分析的`select、poll、epoll`三者之间的区别：

|    对比项    |  Select  |   Poll   |    Epoll     |
| :-------: | :------: | :------: | :----------: |
|  内部数据结构   |   数组位图   |   数组链表   |     红黑树      |
|   最大监听数   |   1024   |  理论无限制   |    理论无限制     |
|  事件查找机制   |   线性轮询   |   线性轮询   | 回调事件直接写回用户态  |
| 事件处理时间复杂度 |   O(n)   |   O(n)   |     O(1)     |
|   性能对比    | `FD`越多越差 | `FD`越多越差 | `FD`增多不会造成影响 |
| `FD`传递机制  |  用~核拷贝   |  用~核拷贝   |    内核维护结构    |

