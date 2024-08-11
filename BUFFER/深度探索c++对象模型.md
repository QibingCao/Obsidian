---

类型: 笔记
创建日期: 2024-08-09
修改日期: 2024-08-09
---

在OO paradigm之中，程序员需要处理一个未知实例，它的类型虽然有所界定，却有无穷可能。这组类型受限于其继承体系，然而该体系理论上没有深度和广度的限制。原则上，被指定的object的真实类型在每一个特定执行点之前，是无法解析的。在C++中，只有通过pointers和references的操作才能够完成。
![[Pasted image 20240809212156.png]]C++以下列方法支持多态：

1 .经由一组隐式的转化操作。例如把一个 derived class 指针转化为一个指向其 public base type的指针：

`shape *ps = new circle();`

- **隐式转换**：在这段代码中，`Circle` 是从 `Shape` 派生的。当你用 `new Circle()` 创建一个 `Circle` 对象时，返回的指针类型是 `Circle*`。但我们将这个指针赋值给了一个 `Shape*` 类型的变量 `ps`。这个过程是隐式的，编译器会自动将 `Circle*` 转换为 `Shape*`，因为 `Circle` 是 `Shape` 的派生类。
- **多态性**：由于 `rotate` 是一个虚函数，调用 `ps->rotate()` 时，会根据对象的实际类型（即 `Circle`）调用派生类中的 `rotate` 方法。
2 .经由 virtual function机制：
`ps->rotate();`
3 .经由 dynamic_cast 和 typeid 运算符：
`if (circle *pc = dynamic_cast<circle*>(ps)) ...`

![[Pasted image 20240809213311.png]]
![[Pasted image 20240809213324.png]]
