---

类型: 笔记
创建日期: 2024-04-04
修改日期: 2024-04-04
---
# 1、大体说下你对 ThreadLocal的理解？

threadLocal对象可以提供线程局部变量，也就是说每个线程拥有一份自己的一个副本变量

多个线程之间互不干扰，一般我们会重写这个initialValue()方法来赋值

每个线程第一次访问get的时候会给线程赋值，就是它使用重写的initiaValue()方法分配数据

  

# 2、ThreadLocal的原理是什么呢？

java8中，每个线程对应的Thread对象内部拥有一个threadLocals字段，这个字段会指向堆中的一个ThreadLocalMap

ThreadLocalMap存储的是当前线程与其threadLocal对象关联的数据

#### 追问1：ThreadLocalMap内存储的是什么？

当前线程与其threadLocal对象关联的数据

Map里面存放的是ThreadLocal对象和线程的变量副本  

#### 追问2：ThreadLocal它是怎样做到线程之间互不干扰的呢？

每个线程对应的Thread对象内部拥有一个threadLocalMap存储数据，

线程访问某个ThreadLocal对象get方法的时候会检测，当前线程的map内是否有key为这个ThreadLocal对象的Entry数据  

如果没有的话，这个ThreadLocal的initiValue()方法创建一个Entry，然后存放到这个ThreadLocalMap里边，

也就是说每个线程他的map内会给对应的ThreadLocal生成他的一份数据放到map内

#### 追问3：老版本JDK的ThreadLocal是怎么设计的呢？

老版本的ThreadLocal中维护了一个大Map，所有的线程的变量都会维护在一个map里面

  

# 3、JDK8 版本的ThreadLocal设计有什么优势相比更早之前的老版本（不指jdk1.7，比jdk1.7还要老，可以认为是ThreadLocal第一版）？

如果沿用1.8之前的，就是他会维护一个大的map，如果线程多的话这个map会很大，不利于维护

新版本中每个线程会维护自己的数据，当线程被销毁的时候，那个对应的ThreadLocalMap就会在下一次GC时被回收

ThreadLocalMap中的Entry，存的这个key是一个弱引用，如果ThreadLocal对象被回收的话，他是不影响的（弱引用不参与root算法）  

  


# 4、ThreadLocalMap 存放数据时，数据的hash值是从Object.hashCode()拿到的，还是其它方式？为什么？

是它自己重写的hashCode()方法  
他好像是以黄金分割数来分割，就是会均匀的分布在Entry数组里面，

使用Object.hashCode()计算出来的是分布不均匀的，如果采用黄金分割数来分配hash值的话，映射到散列表内就是均匀的  

  

# 5、为什么ThreadLocal选择自定义一款Map而没有沿用JDK中的HashMap？

因为它自己重写的话，可以吧这个key限定为特有类型，就是ThreadLocal这个类型  
而且用的key是弱引用，而hashMap使用的key是强引用，弱引用不影响对象被回收的  
ThreadLocalMap，它的写数据和查数据的过程，它有清理过期数据的功能  
能够将发现的过期数据清理掉，从某种意义上说，它也是解决了内存泄露的问题，不会完全解决，  
ThreadLocal的Value的引用，如果对应的数据过期了话，如果不干掉他，可能会出现问题。

  

# 6、每个线程的 ThreadLocalMap对象 是什么时候创建的呢？

每个线程的ThreadLocalMap它是延迟初始化的，就是第一个get或者set的时候，它会检测当前线程是否已经绑定了ThreadLocalMap

如果有的话，他会继续get或者set，如果没有的话，他会先创建。

在线程的生命周期内，ThreadLocalMap只会被初始化一次

  

# 7、ThreadLocalMap 底层存储数据的数组长度 初始化是多少？

它的容量是16

  

#### 追问1：这个数组大小为什么必须为 2的次方数？

和HashMap的设计是一样的，就是为了方便hash寻址，

因为2的次方数减一之后转变成二进制之后是一堆1组成的二进制数，如果数值与这种二进制数进行按位与运算，

所得到的的书一定是大于或者等于0  且  小于这个二进制数值的。

比如使用取模算法

#### 追问2：ThreadLocalMap的扩容阈值是多少呢？

阈值是当前数组，就是Entry**数组长度的2/3**，与HashMap不太一样

#### 追问3：ThreadLocalMap达到扩容阈值一定会扩容么？

这个不会，他会rehash一次，就是会调用一个rehsah方法

会全量扫描整个散列表的逻辑，会把这个过期数据给清理掉，就是重新整理这个散列表

全量扫描完毕之后，当散列表的数据如果仍然达到这个扩容阈值的3/4的话，他才会真正的进行扩容

  

#### 追问4：扩容算法 你简单说一说。

首先会创建一个新的数组，长度是当前散列表数组长度的两倍

然后迭代老的数组，将其中的数据重新按照hash算法放入新的数组里边

迭代完之后，然后更新这个ThreadLocalMap对象的这个散列表引用

它会指向新建的这个数组的“新数组”，最后重新计算下一次触发扩容的阈值。

  

  

# 8、ThreadLocalMap对象的 get逻辑，你说下。

get的时候传进来的肯定是一个ThreadLocal对象，根据这个ThreadLocal对象的hash值按位与当前“数组长度-1”得到一个index

这个散列表数组中下标就是这个index的元素，可能就是要查找的数据，也可能不是，

如果不是的话，说明写数据的时候发生过hash冲突，因为ThreadLocalMap内部类，Entry是没有next字段的，也就是没有办法像HashMap一样冲突后形成一个链表

ThreadLocalMap采用的策略是hash冲突后，线性向后面找到一个合适的位置去写数据，所以get的时候，第一次如果没有命中的话，需要继续向后查找

直到查找到这个数据，或碰到null就结束。

  

#### 追问1：假设get首次未命中，向下迭代查找时，碰到过期数据了，怎么处理？

ThreadLocalMap内存储的是Entry，Entry有两个字段，分别是key和value。key是一个弱引用，指向内存，已经限定类型的ThreadLocal对象

当key对应的ThreadLocal对象被GC回收后，key是弱引用，所以key的get方法，可能会get一个指向null的一个引用。（ThreadLocal被回收后，key.get() == null），就代表这个Entry是过期的

get操作时，碰到过期的数据，它会触发一次“探测式”过期数据回收逻辑

从当前桶位开始向后迭代，将碰到的key == null的Entry置为null，一直迭代到slot == null为止。

  

#### 追问2：探测式清理过期数据，向下迭代过程中碰到正常数据，怎么处理？

如果他碰到了正常数据，会根据它的key来重新计算一个index，看index是否等于当前位置，如果等于，就什么都不做

如果重新计算的index不等于当前为止，那么说明写数据的时候发生过hash冲突，

考虑到当前正在执行清理数据的逻辑，当前slot前面可能有过期数据清理掉，所以这个正常数据需要重新寻找一个更合适的位置去存放

这个位置理论上更接近index，或者等于index

  

# 9、ThreadLocalMap set数据流程，大体说一下。

首先根据key找到对应下标slot，如果slot等于null，就说明当前set方法是新添加数据的逻辑

如果slot不是null，第一种情况。添加新数据逻辑，但是发生hash冲突了

第二种情况，就是更新。如果说当前过程中查找到key与set的key是一致的，那么就会替换掉Entry中的value

如果我们线性查找的过程中，碰到过期数据了，此时它就会做一个替换逻辑

  

#### 追问1：set数据时碰到过期数据了，需要做替换逻辑，这个替换逻辑是怎么做的？

并不是简简单单的替换当前这个位置过期数据，当会以当前位置的下一桶位开始向后去查找，直到碰到null或者key一致是他才会停止

第一种情况，就是它碰到这个key一致时，那么set的这个数据直接就会更新到当前的这个桶位的这个Entry

让当前的Entry与过期的slot位置进行一次互换

第二种情况，遍历到null，也没有找到key一致的数据的话，直接在当前过期桶位直接重写一个Entry，想当于抹除这个过期数据了，然后把新数据放到这个就行了

  
  
作者：zjuttqr  
链接：[https://www.nowcoder.com/discuss/353156786473607168?sourceSSR=search](https://www.nowcoder.com/discuss/353156786473607168?sourceSSR=search)  
来源：牛客网

