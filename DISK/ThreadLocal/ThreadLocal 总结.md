---

类型: 笔记
创建日期: 2024-04-04
修改日期: 2024-04-04
---

![[Pasted image 20240404222813.png]]

# 为什么要设计成弱引用
我们也可以通过**线程池**的角度来想一下为什么作者要把key设计成弱引用，
如果key是强引用，那么Thread-->ThreadLocalMap-->key这个链条会一直存在不会得到回收。
即使在外部这个key对应的ThreadLocal没有引用链了，只要当前Thread不退出，那么该ThreadLocal对应的Entry就不会被回收。

# 为什么要设置为static
### `ThreadLocal`为什么常被声明为`static`

1. **数据隔离**：`ThreadLocal`通常用于维护线程的局部数据，确保**数据在不同线程间是隔离的**。由于`static`变量是属于类的，而不是属于类的某个实例，这样做能够确保**跨不同对象实例**的线程数据隔离。
2. **内存效率**：将`ThreadLocal`声明为`static`意味着无论类的实例被创建多少次，`ThreadLocal`变量都只有一个实例。每个线程都可以访问这个`static` `ThreadLocal`实例，而且每个线程都有自己的线程局部变量副本，从而节省了内存空间。

### `ThreadLocal`非`static`使用场景

1. **实例级别的线程局部变量**：如果你希望线程局部变量的生命周期与你的对象实例相关联，那么可以将`ThreadLocal`声明为非`static`。这种情况下，每个对象实例都会有自己的`ThreadLocal`变量，且每个变量都能为访问它的线程提供独立的数据副本。这在对象需要维护一些与线程相关的状态时特别有用。
2. **减少静态依赖**：在某些设计中，可能希望减少对静态状态的依赖，以提高模块的灵活性和可测试性。在这种情况下，可以将`ThreadLocal`作为对象的一部分，而不是类的静态成员。

