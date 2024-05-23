---

类型: 笔记
创建日期: 2024-05-21
修改日期: 2024-05-21
---
## lab1
### part A
wsl 下long有问题，将a改为quad类型，我好像没这个问题？

compiler中汇编代码生成时+ 和 -代码不对称，我也遇到了这个，我把前面的逻辑改了，把右边的值放入rbx。 
/ 最好用cqto，用rdx没有符号位的扩展？
[x86-64指令含义](https://web.stanford.edu/class/cs107/resources/x86-64-reference.pdf)
### part B
token 没啥说的
lexer 没啥说的

parser 
当以ID开头时，
varDecl 和 statement的界限该如何处理
看你的策略是在边界处，

往后放还是在前面处理
## lab2
手册中 dump classtable 用法写错了
-trace checker.Checker.buildTable

类型检查
构建table符号表，class，method两个级别的表
class：
Binding结构体，可以通过classname找到他的信息

method：
在构建map时，key id和value中的id是有一点关联的。

一开始需要先把class建立起来，method可以边check边做



check.Checker.check中打印了两次program，util里面有个id，里面有个dump id值默认是false，如果打开就会有不同。打开后每个id后面都会跟上一个(%x_2)
在类型检查之后，对ID隐含的值

id类设计的很复杂，在做parse的时候，只要是相同名字符号，new出来的id都是同一个，这样方便统一使用。
为什么要在checker中做个变换呢，函数的声明和函数的调用并不是同一个东西，要相互独立开来。

## lab3 控制流图
总共15个题目，体量大
从3开始提供的代码变少了。

大概率一天做一个题目吧
、

一个function一个控制流图，（让我回想到了boomlab中读汇编代码，很复杂的结构中的各种跳转，整理出来就是一个复杂的控制流图）

控制流图定义和Ast基本上差不多。classes -> funtions -> blocks -> stms
block{ stms, transfer(条件，比如跳转)}

构建继承树

exercise7 要求函数闭合，把java中本身没出现的this指针给加上