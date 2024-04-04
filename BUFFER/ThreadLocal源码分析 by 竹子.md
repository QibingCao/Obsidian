---

类型: 笔记
创建日期: 2024-04-04
修改日期: 2024-04-04
---
[javaguide分析](https://javaguide.cn/java/concurrent/threadlocal.html)
# 引言

满足以下全部三个条件就会发生线程安全问题：
- 多线程
- 共享资源/临界资源
- 非原子性操作
解决线程安全问题就要破坏三个条件：
- 多线程：冲突串行化，例如synchronized，reentrantlcok
- 共享资源/临界资源：**ThreadLocal**
- 非原子性操作：例如使用原子变量，CAS无锁机制
# 1. ThreadLocal概念及使用浅析
ThreadLocal线程本地副本，也叫做线程本地变量，线程局部存储。
在执行时，ThradLocal会为变量在每一条线程创建一个副本，这个副本只有每条线程自己可以访问。
```java
// 省略方法体(后面源码再详细分析)
public class ThreadLocal<T> {
    // 构造函数
    public ThreadLocal() {}
    
    // 初始化方法：在创建ThreadLocal对象时可以使用该方法进行初始化设值
    protected T initialValue()
    
    // 获取ThreadLocal在**当前线程**中保存的变量副本
    public T get() 
    
    // 设置**当前线程**中变量的副本
    public void set(T value)
    
    // 移除当前线程中变量的副本
    public void remove()
    
    // 内部子类：扩展了ThreadLocal的初始化值的方法，支持Lambda表达式赋值
    static final class SuppliedThreadLocal<T> extends ThreadLocal<T>
    
    // 内部类：定制的hashMap，仅用于维护当前线程的本地变量值。
    // 仅ThreadLocal类对其有操作权限，是Thread的私有属性。
    // 为避免占用空间较大或生命周期较长的数据常驻于内存引发一系列问题，
    // hashtable的key是弱引用WeakReferences。
    // 当堆空间不足时，会清理未被引用的entry。
    static class ThreadLocalMap
    
    // 省略其他代码.......
}

```
在创建ThreadLocal对象时可以`initialValue()`对变量副本初始化，也可以使用`set()`方法更改值，使用`get()`方法获取值，`remove()`移除值。
## 1.1 使用例子
对于像DB连接Connection这样的对象，每个线程都要去访问，并且每次访问都要是自己之前的，这样的情况就适合用ThreadLocal
```java
public class DBUtils {
	// 一般ThreadLocal都会设置为private static final
    private static ThreadLocal<Connection> connectionHolder =
    new ThreadLocal<Connection>(){
        @SneakyThrows
        public Connection initialValue(){
            return DriverManager.getConnection(
                    "jdbc:mysql:127.0.0.1:3306/test?user=root＆password=root");
        }
    };

    public static Connection getConnection() throws SQLException {
        return connectionHolder.get();
    }
}

```
## 1.2 ThreadLocal使用场景
- 上下文传递。一个对象需要在多个方法中层次传递使用，比如用户身份、任务信息、调用链ID、关联ID（如日志的uniqueID，方便串起多个日志）等，如果此时使用责任链模式给每个方法添加一个context参数会比较麻烦，而此时就可以使用ThreadLocal设置参数，需要时get以下即可。
- 线程间的数据隔离。spring事务管理机制实现使用ThreadLocal来保证单线程中的数据库操作使用的是同一个数据库连接。同时，采用这种方式可以使业务层使用事务时不需要感知并管理Connection对象，通过传播级别，能够巧妙地管理多个事务配置之间的切换、挂起和恢复。
- ThreadLocal一般情况下，在项目开发时很少使用，在框架源码中应用较多。如Spring框架的事务隔离机制中的`TransactionSynchronizationManager`类，也包括Netty框架中的二次封装类`FastThreadLocal`等。
```java
/**
*
* 黑马点评中使用ThreadLocal处理用户登录信息
*/

//使用方式
UserHolder.getUser()
UserHolder.saveUser()
UserHolder.removeUser()

// 用户登录信息
public class UserHolder {  
	private static final ThreadLocal<UserDTO> tl = new ThreadLocal<>();  
	
	public static void saveUser(UserDTO user){  
		tl.set(user);  
	}  
  
	public static UserDTO getUser(){  
		return tl.get();  
	}  
  
	public static void removeUser(){  
		tl.remove();  
	}  
}
```
# ThreadLocal原理分析
## 2.1 创建副本原理
```java
// ThreadLocal类 → set()方法
public void set(T value) {

    // 获取当前执行线程
    Thread t = Thread.currentThread();
    
    // 获取当前线程的threadlocals成员变量
    ThreadLocalMap map = getMap(t);
    
    // 如果map不为空，则将value添加进map
    if (map != null)
        map.set(this, value); // this 为静态的ThreadLocal对象
    // 如果map为空则先为当前线程创建一个map再将value加入map
    else
        createMap(t, value);
}

```
`ThreadLocal.set()`方法中总归来说分为三步：
- 调用`getMap()`获取当前线程的ThreadLocalMap
- 如果map不为空则将传入的`value`值添加进map
- 如果map为空则先为当前线程创建一个map再将`value`加入map
看看`getMap(Thread)`方法：
```java
// ThreadLocal类 → getMap()方法 
ThreadLocalMap getMap(Thread t) { // t为当前线程
	return t.threadLocals; 
}
```
可以看到，`Thread`类的成员变量`threadLocals`实则就是`ThreadLocalMap`，而`ThreadLocalMap`则是一个给`ThreadLocal`定制版的`HashMap`，也是`ThreadLocal`的内部类，如下：
```java
// ThreadLocal类
public class ThreadLocal<T> {

    // ThreadLocal内部类：ThreadLocalMap
    static class ThreadLocalMap {
        // ThreadLocalMap内部类：Entry
        static class Entry extends WeakReference<ThreadLocal<?>> {
            Object value;
            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
    }
}

```
在`ThreadLocalMap`类中还存在一个内部类`Entry`，继承自`WeakReference`弱引用类型，结构如下：
![[Pasted image 20240404152909.png]]
再来看看`createMap()`方法：
```java
// ThreadLocal类 → createMap()方法
void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}

```
### 创建副本原理总结
1. 每条线程Thread内部有一个`ThreadLocal.ThreadLocalMap`类型的成员变量`threadLocals`
2. `threadLocals`是每条线程用来存放副本的，key为当前`ThreadLocal`对象，value为`T`类型的变量
3. 每个线程`threadLocals`最初为空，当调用到`ThreadLocal.set()`或`.get()`方法是，调用`createMap()`方法进行初始化。
4. 在当前线程中使用副本变量，通过`get`方法在`ThreadLocals`中查找。
![[Pasted image 20240404161143.png]]

![[Pasted image 20240404153452.png]]
## 2.2 ThreadLocal获取变量副本原理分析
```java
// ThreadLocal类 → get()方法
public T get() {

    // 获取当前执行线程
    Thread t = Thread.currentThread();
    
    // 获取当前线程的ThreadLocalMap
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        // 如果map不为空，将当前ThreadLocal对象作为key获取对应值
        ThreadLocalMap.Entry e = map.getEntry(this);
        // 如果获取的值不为空则返回获取到的value
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    // 如果map为空则调用setInitialValue方法
    return setInitialValue();
}

```
在`ThreadLocal.get()`方法中，总归来说分为三步：
- 获取到当前执行线程，通过`getMap(Thread)`方法获取`ThreadLocalMap`类型的map
- 将当前`ThreadLocal`对象this作为key尝试获取map中的`<key,value>`键值对，获取成功返回value
- 如果第一步获取的map为空则调用`setInitialValue()`方法返回value
调用`get()`方法之后首先会获取当前线程的`threadLocals`成员变量(即`ThreadLocalMap`)，如map不为空则以为`this`作为key获取`ThreadLocal`中存储的变量副本，如果为空则调用`setInitialValue()`方法：
```java
// ThreadLocal类 → setInitialValue()方法
private T setInitialValue() {
    // 获取ThreadLocal初始化值
    T value = initialValue(); // 上文创建ThreadLocal对象时要override
    Thread t = Thread.currentThread();
    // 获取当前线程的map
    ThreadLocalMap map = getMap(t);
    
    // 如果map不为空则将初始化值添加进map容器
    if (map != null)
        map.set(this, value);
    // 如果map为空则创建一个ThreadLocalMap容器
    else
        createMap(t, value);
    return value;
}

// ThreadLocal类 → initialValue()()方法
protected T initialValue() {
    return null;
}

```
在`setInitialValue()`方法中首先会调用`initialValue()`方法获取初始化值，而`initialValue()`方法默认是返回空的，
但是`initialValue()`方法可以在创建`ThreadLocal`对象时进行重写，如下：
```java
private static ThreadLocal<Object> threadlocal =
new ThreadLocal<Object>(){
    @SneakyThrows
    public Object initialValue(){
        return new Object();
    }
};

```
获取到初始化的值之后，再次获取当前线程的`threadLocals`，如果不为空则以this为key，初始值为value添加进map。如果当前线程的`threadLocals`为空，则先调用`createMap(t, value);`为当前线程创建一个`ThreadLocalMap`并将this和初始值以k-v形式加入map中，然后并将value返回，如果没有创建`ThreadLocal`对象时吗没有初始化值则返回null，至此整个`ThreadLocal.get()`方法结束。如下：![[Pasted image 20240404154106.png]]
# InheritableThreadLocal详解
通过上述的分析，不难得知ThreadLocal设计的目的就是为每条线程都开辟一块自己的局部变量存储区域(并不是为了解决线程安全问题设计的，不过使用ThreadLocal可以避免一定的线程安全问题产生).
所以如果你想要将ThreadLocal中的数据共享给子线程时，实现起来将额外的困难。
而InheritableThreadLocal则应运而生，InheritableThreadLocal可以实现多个线程访问ThreadLocal的值。
```java
private static InheritableThreadLocal<String> itl = 
new InheritableThreadLocal<String>();
public static void main(String[] args) throws InterruptedException {
    System.out.println(Thread.currentThread().getName()
    + "......线程执行......");
    itl.set("竹子....");
    System.out.println("父线程：main线程赋值：竹子....");
    new Thread(()->{
        System.out.println(Thread.currentThread().getName()
        + "......线程执行......");
        System.out.println("子线程：T1线程读值："+itl.get());
    },"T1").start();
    System.out.println("执行结束.....");
}

```
![[Pasted image 20240404154527.png]]
从结果中不难看出，子线程`T1`读取的值竟然是`main`父线程设置的值，这是为什么呢？下面我们看看`InheritableThreadLocal`的源码：
```java
public class InheritableThreadLocal<T> extends ThreadLocal<T> {
    // 在父线程向子线程复制InheritableThreadLocal变量时使用
    protected T childValue(T parentValue) {
        return parentValue;
    }
    // 返回线程的inheritableThreadLocals成员变量
    ThreadLocalMap getMap(Thread t) {
       return t.inheritableThreadLocals;
    }
    // 为线程的成员变量inheritableThreadLocals进行初始化
    void createMap(Thread t, T firstValue) {
        t.inheritableThreadLocals = new ThreadLocalMap(this, firstValue);
    }
}

```
在`InheritableThreadLocal`中重写了父类`ThreadLocal`的`getMap()`以及`createMap()`方法，在我们前面分析`ThreadLocal`时，曾提到过线程类`Thread`中存在一个成员变量`threadlocals`，而实则`Thread`中除开`threadlocals`成员之外，还存在另外一个成员变量`inheritableThreadLocals`，如下：
```java
ThreadLocal.ThreadLocalMap threadLocals = null;
ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
```
所以当操作`InheritableThreadLocal`变量时只影响线程的`inheritableThreadLocals`成员，而并不影响`threadlocals`成员。
## 3.1 InheritableThreadLocal父子线程传值原理
搞清楚`InheritableThreadLocal`构成之后，我们接着来分析一下父子线程传值究竟是如何实现的。我们一般在创建子线程时，都是直接选择`new Thread()`创建：
```java
Thread t1 = new Thread();

```
接着会调用`Thread`类的构造函数创建线程对象：
```java
// Thread类 → 构造函数
public Thread() {
    init(null, null, "Thread-" + nextThreadNum(), 0);
}

// Thread类 → init()方法重载
private void init(ThreadGroup g, Runnable target, String name,
                long stackSize) {
    // 调用全参的init方法完成线程初始化
    init(g, target, name, stackSize, null, true);
}

// Thread类 → init()方法
private void init(ThreadGroup g, Runnable target, String name,
                  long stackSize, AccessControlContext acc,
                  boolean inheritThreadLocals) {
    if (name == null) {
        throw new NullPointerException("name cannot be null");
    }
    this.name = name;
    // 获取当前执行线程作为父线程
    Thread parent = currentThread();
    SecurityManager security = System.getSecurityManager();
    if (g == null) {
        // 确认创建出的线程是否为子线程
        // 如果SecurityManager不为空则获取SecurityManager的线程分组
        if (security != null) {
            g = security.getThreadGroup();
        }

        // 如果SecurityManager中没有为创建出的线程设置线程分组，
        // 则使用当前执行的线程parent的父线程组
        if (g == null) {
            g = parent.getThreadGroup();
        }
    }

    // 无论是否显式传入threadgroup，都要检查访问
    g.checkAccess();

    // 如果SecurityManager不为空则检查权限是否
    // 为SUBCLASS_IMPLEMENTATION_PERMISSION
    if (security != null) {
        if (isCCLOverridden(getClass())) {
            security.checkPermission(SUBCLASS_IMPLEMENTATION_PERMISSION);
        }
    }
    g.addUnstarted();
    // 将当前执行线程设置为创建出的线程的父线程
    this.group = g;
    this.daemon = parent.isDaemon();
    this.priority = parent.getPriority();
    // 获取线程上下文类加载器
    if (security == null || isCCLOverridden(parent.getClass()))
        this.contextClassLoader = parent.getContextClassLoader();
    else
        this.contextClassLoader = parent.contextClassLoader;
    // 为当前创建出的线程设置线程上下文类加载器
    this.inheritedAccessControlContext =
            acc != null ? acc : AccessController.getContext();
    this.target = target;
    setPriority(priority);
    // ***重点！！！后面详细分析***
    if (inheritThreadLocals && parent.inheritableThreadLocals != null)
        this.inheritableThreadLocals =
            ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);
    // 为创建出的线程分配默认线程栈大小
    this.stackSize = stackSize;
    // 设置线程ID
    tid = nextThreadID();
}

```
如上便是线程创建时的初始化过程，在`init()`方法中有这么一段代码：
```java
if (inheritThreadLocals && parent.inheritableThreadLocals != null)
        this.inheritableThreadLocals =
                  ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);

```
当采用默认方式创建子线程时，一条线程执行new指令创建Thread对象的方式被称为默认方式，会将当前执行创建逻辑的线程设置为创建出来的线程的父线程。如果父线程的`inheritableThreadLocals`成员变量不为空，那么则会执行`this.inheritableThreadLocals=ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);`，将父线程`inheritableThreadLocals`传递至子线程。接着可以再看看`ThreadLocal.createInheritedMap()`方法：
```java
// ThreadLocal类 -> createInheritedMap()方法
static ThreadLocalMap createInheritedMap(ThreadLocalMap parentMap) {
    return new ThreadLocalMap(parentMap);
}

//  ThreadLocalMap类 -> 私有构造函数
// 构建一个包含所有parentMap中Inheritable ThreadLocals的ThreadLocalMap
// 该函数只被createInheritedMap()调用.
private ThreadLocalMap(ThreadLocalMap parentMap) {
    // 获取父线程的所有Entry
    Entry[] parentTable = parentMap.table;
    // 获取父线程的Entry数量
    int len = parentTable.length;
    setThreshold(len);
    // ThreadLocalMap使用Entry[] table存储ThreadLocal
    table = new Entry[len];

    // 挨个复制父线程中map的Entry
    for (int j = 0; j < len; j++) {
        Entry e = parentTable[j];
        if (e != null) {
            @SuppressWarnings("unchecked")
            ThreadLocal<Object> key = (ThreadLocal<Object>) e.get();
            if (key != null) {
                // 为什么这里不是直接赋值而是使用childValue方法？
                // 因为childValue内部是直接将e.value返回的，
                // 这样实现的目的可能是为了保证代码最大程度上的拓展性
                // 因为可以重写childValue()覆盖
                Object value = key.childValue(e.value);
                Entry c = new Entry(key, value);
                int h = key.threadLocalHashCode & (len - 1);
                while (table[h] != null)
                    h = nextIndex(h, len);
                table[h] = c;
                size++;
            }
        }
    }
}

```
当调用`ThreadLocal.createInheritedMap()`方法后会将父线程中`inheritableThreadLocals`成员的所有`Entry`全部复制一遍给子线程的`inheritableThreadLocals`成员，至此，整个创建过程完成。从这个流程中我们可以得知：父子线程传值的实现是通过创建线程时复制`inheritableThreadLocals`的所有`Entry`实现的。
# 4. ThreadLocalMap原理剖析
`ThreadLocal`的原理是涉及三个核心类：`ThreadLocal`、`Thread`以及`ThreadLocalMap`类。
在`Thread`类中存在两个成员变量：`threadLocals`与`inheritableThreadLocals`，这两个成员变量的类型都为`ThreadLocalMap`，经过一系列分析后我们可以得知，这两个成员变量是存储线程变量副本的最终容器。
而前面也曾提到过：`ThreadLocalMap`是`ThreadLocal`中定制版的`HashMap`，但是它并没有实现`Map`接口，而是自己内部通过数组类型存储`Entry`实现。
而`Entry`只是简单的继承了`WeakReference`软引用，并没有没有实现类似`HashMap`中`Node.next`的后继节点指向，所以`ThreadLocalMap`并不是链表形式的实现。
哪没有了链表结构之后，`ThreadLocalMap`是如何解决哈希冲突的呢？下面可以从源码角度分析得知：
```java
// ThreadLocalMap类 → Entry静态内部类
static class Entry extends WeakReference<ThreadLocal<?>> {
    // value：存储的变量副本
    Object value;
    Entry(ThreadLocal<?> k, Object v) {
           super(k);
           value = v;
    }
}

// ThreadLocalMap类 → 构造方法
ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
    // 成员变量table(数组结构)，INITIAL_CAPACITY值为16的常量
    table = new Entry[INITIAL_CAPACITY];
    // 位运算，简化取模运算，计算出需要存放的位置
    int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
    table[i] = new Entry(firstKey, firstValue);
    size = 1;
    setThreshold(INITIAL_CAPACITY);
}

```
在调用`createMap()`方法创建`ThreadLocalMap`示例时，在`ThreadLocalMap`的构造方法中，会为成员变量`table`初始化一个长度为**16**的`Entry`数组，通过`hashCode`与`length`位运算确定出一个下标索引值`i`，这个`i`就是被存储在`table`数组中的下标位置。

```java
  //ThreadLocalMap类 → set()方法
  private void set(ThreadLocal<?> key, Object value) {
    // 获取table及其长度
    Entry[] tab = table;
    int len = tab.length;
    // 使用key的哈希值和数组长度计算获取索引值
    int i = key.threadLocalHashCode & (len-1);

    // 遍历table如果已经存在则更新值，不存在则创建
    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) { // 开放定址法
        ThreadLocal<?> k = e.get();
        // 如果key相同，则使用新value替换老value
        if (k == key) {
            e.value = value;
            return;
        }
        // 如果table[i]为空则创建新的Entry存储
        if (k == null) {
            replaceStaleEntry(key, value, i);
            return;
        }
    }
    
    // table[i]不为null且key不相同的情况下，
    // 如果遍历完数组也没有找到为null的位置，
    // 则代表数组需要扩容，则将数组扩容两倍
    tab[i] = new Entry(key, value);
    int sz = ++size;
    // 如果清理过期的数据之后，数组内的可用数据还占
    // 3/4的情况下，直接扩容两倍
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
}

```
我们可以从源码中不难发现，在`set()`方法开始后，会首先获取`table`的长度和`ThreadLocal`对象的哈希值用于计算出一个下标索引值`i`：`int i = key.threadLocalHashCode & (len-1);`。
```java
// ThreadLocal中threadLocalHashCode相关代码
private final int threadLocalHashCode = nextHashCode();

private static AtomicInteger nextHashCode =
    new AtomicInteger();

// 0x61c88647为斐波那契散列乘数，哈希得到的结果会比较分散
private static final int HASH_INCREMENT = 0x61c88647;

private static int nextHashCode() {
    // 原子计数器自增
    return nextHashCode.getAndAdd(HASH_INCREMENT);
}

```
因为`ThreadLocal`中哈希码相关的成员都是静态`static`关键字修饰的原因，每次创建`ThreadLocal`对象时，都会在对象初始化的时候调用一次自增方法为`ThreadLocal`对象生成一个哈希值。
而`HASH_INCREMENT=0x61c88647`是因为`0x61c88647`为斐波那契散列乘数，通过它散列(hash)出来的结果分布会比较均匀，可以很大程度上避免hash冲突。  

**ThreadLocalMap使用的是开放定址法，如果hashcode对应位置有元素则遍历寻找下一个空位置**
**当key为空，value不为空时，插入之前先将value清理了。**
![[Pasted image 20240404160244.png]]
## ThreadLocalMap总结
经过如上分析我们能够得到一个结论：每条线程的`threadlocals`都会在内部维护独立`table`数组，而每个`ThreadLocal`对象在不同的线程`table`中位置都是相同的。对于同一条线程而言，不同的`ThreadLocal`变量副本都会被封装成一个个的`Entry`对象存储在自己内部的`table`中。
# 5. ThreadLocal注意事项
## 5.1 ThreadLocal线程安全问题
ThreadLocal虽然能够在一定程度上解决线程安全问题，但ThreadLocal设计的初衷是为每条线程开辟一块自己的存储空间。
所以如果`ThreadLocal.set()`的对象如果是共享的，多线程情况下也会造成线程安全问题的出现。
## 5.2 ThreadLocal副本变量的产生
ThreadLocal的变量并不是每条线程拷贝克隆一个对象，而是每个线程新建一个。
## 5.3 ThreadLocal在线程池情况下可能会产生脏数据
因为线程池会复用线程，而线程上一个执行的任务对ThreadLocal进行`set()`操作后，在线程`run()`结束后没有调用`remove()`移除变量副本，下个`Runnable`任务如果直接对ThreadLocal进行`get()`操作则可能读到脏数据。
## 5.4 ThreadLocal可能会造成内存泄露

ThreadLocalMap中存储变量副本时，Entry对象使用ThreadLocal的弱引用作为key。
如果一个ThreadLocal对象没有外部强引用来指向它，在堆内存不足时GC机制会回收掉这些弱引用类型的**key**（注意只回收key），则会造成`ThreadLocalMap<null,Object>`的情况。
同时线程也迟迟不结束(比如线程池中的常驻线程)，那么这些`key=null`的value值则会一直存在一条强引用链：
`Thread.threadlocals(Reference)成员变量 -> ThreadLocalMap对象 -> Entry对象 -> Object value对象`
![[Pasted image 20240404161024.png]]
导致GC无法回收造成内存泄露，这个Object就是泄露的对象。
### 解决内存泄漏
我们可以在使用完ThreadLocal手动调用`ThreadLocal.remove()`方法清空ThreadLocal变量副本即可解决。