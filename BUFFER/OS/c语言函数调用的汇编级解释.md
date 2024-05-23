---

类型: 随想
创建日期: 2024-05-02
修改日期: 2024-05-02
---
函数调用栈指针EBP，ESP
1. **ESP (Stack Pointer, 栈指针)**: ESP 寄存器存储的是当前栈顶的地址。在函数调用时，ESP 用来指向栈中最后一个被推入的元素。每当在栈上压入新数据时（如调用函数时传递参数），ESP 的值会递减（因为栈在内存中通常是向下增长的）；每当从栈上弹出数据时（如函数返回后清理栈），ESP 的值则会递增。
2. **EBP (Base Pointer, 基指针)**: EBP 寄存器通常用于在函数调用期间作为一个参考点，来访问函数的参数和局部变量。**函数在调用开始时通常会把当前的 EBP 值压入栈中**，并将 ESP 的当前值赋给 EBP，这样不论 ESP 如何变化，函数内部通过 EBP 都可以稳定地访问到其参数和局部变量。这种方式被称为帧指针（Frame Pointer）技术。

当讨论到“保存的 EBP”时，通常是指在函数调用时发生的操作。在调用新函数前，当前函数的 EBP 值会被推入栈中，这是为了在函数返回时可以恢复这个 EBP 值，保证栈的结构不被破坏。这个保存的 EBP 值，也就是被调用函数的调用者的 EBP，存放在被调用函数的栈帧中，它是连接当前函数栈帧和上一个函数栈帧的关键链接。
这种机制使得函数调用链可以在返回时正确地恢复各个函数的栈环境，支持多层函数调用和返回的过程。
![[Pasted image 20240502163821.png]]

![[Pasted image 20240502162640.png]]

![[Pasted image 20240502163338.png]]

## CSAPP书中介绍
csapp书第3章中有对为什么用rbp有详细的介绍，简单来说是最简单的栈帧大小恒定，只需要一个rsp便可以解决问题，但遇到栈帧变长的（比如申请数组，比如传递参数大于6寄存器放不下），所以用rbp存放栈底地址。
## bomblab例子
在[[bomblab#phase_2]]中，
以ebp为基数：
	-0x1c(%ebp) ebp减为局部变量
	0x8(%ebp) ebp加为函数输入参数
以esp为基数：（第一个参数一般都是 mov %eax,(%esp)，直接保存在栈顶 )
	一般都是给要调用的函数准备输入参数
```python
08048ba4 <phase_2>:                                                         ; 6个数字规律
 8048ba4:	55                   	push   %ebp                             ; 保存旧的ebp
 8048ba5:	89 e5                	mov    %esp,%ebp                        ; ebp指向旧的ebp
 8048ba7:	83 ec 28             	sub    $0x28,%esp                       ; 分配内存空间，向低地址扩张


 8048baa:	8d 45 e4             	lea    -0x1c(%ebp),%eax                 ; -0x1c(%ebp) -> array start
 8048bad:	89 44 24 04          	mov    %eax,0x4(%esp)                   ; array start 传参
 8048bb1:	8b 45 08             	mov    0x8(%ebp),%eax                   
 8048bb4:	89 04 24             	mov    %eax,(%esp)                      ; main函数传给他的参数 传参
 8048bb7:	e8 b0 04 00 00       	call   804906c <read_six_numbers>       ; call

 8048bbc:	c7 45 fc 01 00 00 00 	movl   $0x1,-0x4(%ebp)                  ; -0x4(%ebp) -> count = 1
 8048bc3:	eb 1e                	jmp    8048be3 <phase_2+0x3f>           ; jmp be3


 8048bc5:	8b 45 fc             	mov    -0x4(%ebp),%eax                  ; tmp = count
 8048bc8:	8b 54 85 e4          	mov    -0x1c(%ebp,%eax,4),%edx          ; edx = array[count]
 8048bcc:	8b 45 fc             	mov    -0x4(%ebp),%eax
 8048bcf:	48                   	dec    %eax                             ; tmp--
 8048bd0:	8b 44 85 e4          	mov    -0x1c(%ebp,%eax,4),%eax          ; eax = array[tmp]
 8048bd4:	83 c0 05             	add    $0x5,%eax                        ; eax += 5

 8048bd7:	39 c2                	cmp    %eax,%edx                        ; if eax == edx
 8048bd9:	74 05                	je     8048be0 <phase_2+0x3c>           ; 	jmp be0
 8048bdb:	e8 e6 0a 00 00       	call   80496c6 <explode_bomb>

 8048be0:	ff 45 fc             	incl   -0x4(%ebp)                       ; count++


 8048be3:	83 7d fc 05          	cmpl   $0x5,-0x4(%ebp)                 ; if count <= 5
 8048be7:	7e dc                	jle    8048bc5 <phase_2+0x21>          ; 	jmp bc5 // 继续循环


 8048be9:	c9                   	leave                                   ; 恢复esp，ebp中的值
 8048bea:	c3                   	ret   

```