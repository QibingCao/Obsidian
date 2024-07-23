---

类型: 笔记
创建日期: 2024-05-13
修改日期: 2024-05-13
---
## Part A
### exercise 1
>What is this program's output?

8 7
80
### exercise 6
![[Pasted image 20240513195518.png]]
### exercise 7
修改了生成汇编代码的部分，默认将left结果放入%rax，right结果放入%rbx，此次操作最终结果放入%rax。

对于示例1：
![[Pasted image 20240513195547.png]]
## Part B
### exercise 8
将MiniJava中出现的kind添加至ENUM，此处我将"System"，"out"，"println"等词（非关键词）也加入了ENUM以便后续操作。
### exercise 9
为方便读取字符后再放回fileStream，我将原本的入参InputStream改为PushbackInputStream。
PushbackInputStream中unread()方法可以放回stream。
### exercise 10
#### javac
![[Pasted image 20240513194303.png]]
#### Parser 
##### without dump token
![[Pasted image 20240513194246.png]]
##### with dump token
![[Pasted image 20240513194414.png]]
### Challenge
记录了token的结束位置
## Part C
### exercise 12
Lexer和Parser中统一使用`util.Error(String errMsg)`，输出详细报错信息和报错位置。
例如：
![[Pasted image 20240513200716.png]]
![[Pasted image 20240513200727.png]]

![[Pasted image 20240513200900.png]]
![[Pasted image 20240513200911.png]]