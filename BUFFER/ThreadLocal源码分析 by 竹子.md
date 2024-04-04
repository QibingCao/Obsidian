---

类型: 笔记
创建日期: 2024-04-04
修改日期: 2024-04-04
---
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
![[Pasted image 20240404153452.png]]
## 2.2 ThreadLocal获取变量副本原理分析
