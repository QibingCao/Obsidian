---

类型: 笔记
创建日期: 2024-05-14
修改日期: 2024-05-14
---
### 动机
为什么是树，因为树结构方便递归
例子：S分支1
```
\
 \
 "="
 /  \
 x   E (一整棵树)
	  \
	  ...
```
用于编码语法的树。（程序的本质就是一种编码形式）
一般情况下是一棵k叉树。
为什么叫抽象语法树：抽象体现在抽离出原语法的形式，只保留有用的语法信息。用一颗简化的树来完整表示整个语法树
### 数据结构（Java）
实现技术：（工具的选择很大程度上影响你的体验）
	lab早期版本（java8）：局部类层次 = 抽象基类 + 子类  --> 代码很绕，实现不美观
	 当前lab（java22）：密封接口 + 记录（比java8代码量少50%）
		 将每个非终结符（例如S）定义为接口类，右部为实现接口的record
```java
sealed interface S permits ...{}; // 密封接口
record ASSIGN(String x, E e) implements S{}
record Print(E e) implements S{}
record Sequence(S s1, S s2) ...{}


interface E{};
record Num(int n){}
record add(E left, E right){}
...
```
java要求一个文件内只能写一个public类，所以lab代码中写了静态内部类，看起来变丑了
为什么用record而不是类：
	record可用switch case模式匹配
为什么不是抽象基类：
	record的父类必须是Object
### 语法树的生成/构建
在原有的parser中构建语法树结构的返回值
