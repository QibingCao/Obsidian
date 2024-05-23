---

类型: 笔记
创建日期: 2024-05-19
修改日期: 2024-05-19
---
我直接`objdump -d ./bomb > assembly.asm` 看汇编。
[GDB cheatsheet](https://darkdust.net/files/GDB%20Cheat%20Sheet.pdf) 中有GDB简单调试命令的汇总，其中使用的到多为：
`x/nfu memAddr`Print memory. 
n: How many units to print (default 1). 
f: Format character (like „print“). 
	a Pointer. 
	...
u: Unit. Unit is one of: 
	b: Byte, 
	h: Half-word (two bytes) 
	w: Word (four bytes) 
	...
## phase_1
逻辑是简单的字符串比较，调用`strings_not_equal`，比较输入字符串和`0x80499b8`地址字符串的值是否相同。
`(gdb) x/s 0x80499b8`
`0x80499b8:      "The future will be better tomorrow."`
结束
## phase_2
读取6个数字，并判断前一个是否是后一个+5。
## phase_3
switch case
## phase_4
fun4递归求阶乘，判断输入是否是10的阶乘
## phase_5

`(gdb) x/18xw 0x804a5c0`
```
(gdb) x/18xw 0x804a5c0

0x804a5c0 <array.2505>: 0x0000000a      0x00000002      0x0000000e 0x00000007
0x804a5d0 <array.2505+16>:      0x00000008      0x0000000c      0x0000000f 0x0000000b
0x804a5e0 <array.2505+32>:      0x00000000      0x00000004      0x00000001 0x0000000d
0x804a5f0 <array.2505+48>:      0x00000003      0x00000009      0x00000006 0x00000005
0x804a600 <node9>:      0x000002dc      0x00000009

------------------------------
array  0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15
value 10  2 14  7  8 12 15 11  0  4   1 13  3  9  6  5
```

伪代码：
```python
15 + 6 + 14 + 2 + 1 + 10 + 0 + 8 + 4 + 9 + 13 + 11

入参：
-0x14(%ebp) -> int a, -0x18(%ebp) -> int b
代码：
-0x10(%ebp) -> int count = 0, -0xc(%ebp) -> int sum = 0

while a != 15
	count++
	a = array[a]
	sum += a
if count == 12
	if b == sum
		return true
```
## phase_6
伪代码：
```python
0x804a66c -> addr origin

-0x8(%ebp) -> addr start = origin

string input 
int in = atoi(input)
Memory[start] = in
start = fun6(start)
-0x4(%ebp) -> addr n
n = start
-0xc(%ebp) -> int count = 1
while count <= 8
	n = Memory[n + 2]
	count++
if Memory[origin] == Memory[n]
	return true
```
fun6降序排列链表
```python
.main:
	-0x10(%ebp) -> addr start
	-0xc(%ebp) -> addr next = Memory[start + 2]
	Memory[start + 2] = 0
	jmp eab
	
.e49:
	-0x4(%ebp) -> addr a1 = start
	-0x8(%ebp) -> addr a2 = start
	jmp e66

.e57:
	a2 = a1
	a1 = Memory[a1 + 2]

.e66:
	if a1 == 0:
		jmp e7a
	if Memory[next] > Memory[a1]:
		jmp .e57
		
.e7a:
	if a1 == a2:
		jmp e8d
	Memory[a2 + 2] = next
	jmp .e93
	

.e8d:
	start = next
.e93:
	a2 = Memory[next + 2]
	Memory[next + 2] = a1
	next = a2

.eab:
	if next != 0:
		jmp e49
	return start
```

```
  address        node      val      id            next        prevVal
0x804a66c           0        0       0      0x0804a660           1001
0x804a660           1      149       1      0x0804a654              0
0x804a654           2      509       2      0x0804a648            149
0x804a648           3      285       3      0x0804a63c            509
0x804a63c           4      565       4      0x0804a630            285
0x804a630           5      251       5      0x0804a624            565
0x804a624           6      323       6      0x0804a618            251
0x804a618           7      991       7      0x0804a60c            323
0x804a60c           8      773       8      0x0804a600            991
0x804a600           9      732       9      0x00000000            773
```

降序第九个，累了。。。
## phase_7
降序的二叉搜索树中查找元素。
伪代码：
```
.fun7
	fun7(input, node)
		if node == 0:
			return 0xfff...
		if input <= node.val:
			jmp .f54
		output = fun7(input, node.left)
		return output*2
.f54
	if input != node .val:
		jmp f67
	return 0

.f67
	output = fun7(input, node.right)
	return output*2 + 1
```

`(gdb) x/16aw 0x804a720`
```
0x804a720 <n1>: 0x24    0x804a714 <n21> 0x804a708 <n22> 0x0

0x804a714 <n21>:        0x8     0x804a6e4 <n31> 0x804a6fc <n32> 0x24
0x804a708 <n22>:        0x32    0x804a6f0 <n33> 0x804a6d8 <n34> 0x8

0x804a6e4 <n31>:        0x6     0x804a6c0 <n41> 0x804a69c <n42> 0x2d
0x804a6fc <n32>:        0x16    0x804a690 <n43> 0x804a6a8 <n44> 0x32
0x804a6f0 <n33>:        0x2d    0x804a6cc <n45> 0x804a684 <n46> 0x16
0x804a6d8 <n34>:        0x6b    0x804a6b4 <n47> 0x804a678 <n48> 0x6

0x804a6c0 <n41>:        0x1     0x0     0x0     0x28
0x804a69c <n42>:        0x7     0x0     0x0     0x23
0x804a690 <n43>:        0x14    0x0     0x0     0x7
0x804a6a8 <n44>:        0x23    0x0     0x0     0x63
0x804a6cc <n45>:        0x28    0x0     0x0     0x6b
0x804a684 <n46>:        0x2f    0x0     0x0     0x14
0x804a6b4 <n47>:        0x63    0x0     0x0     0x1
0x804a678 <n48>:        0x3e9   0x0     0x0     0x2f
```
## 附录
`assembly.asm`
```
08048b80 <phase_1>:                                                        ; 比较字符串
 8048b80:	55                   	push   %ebp
 8048b81:	89 e5                	mov    %esp,%ebp
 8048b83:	83 ec 08             	sub    $0x8,%esp
 8048b86:	c7 44 24 04 b8 99 04 	movl   $0x80499b8,0x4(%esp)            ; 0x80499b8 传参
 8048b8d:	08 
 8048b8e:	8b 45 08             	mov    0x8(%ebp),%eax                  ; (%ebp)+8 内容传参
 8048b91:	89 04 24             	mov    %eax,(%esp)
 8048b94:	e8 66 05 00 00       	call   80490ff <strings_not_equal>     ;  call strings_not_equal


 8048b99:	85 c0                	test   %eax,%eax                        ; if output == 0
 8048b9b:	74 05                	je     8048ba2 <phase_1+0x22>           ; 	return true
 8048b9d:	e8 24 0b 00 00       	call   80496c6 <explode_bomb>
 8048ba2:	c9                   	leave  
 8048ba3:	c3                   	ret    

08048ba4 <phase_2>:                                                         ; 6个数字规律
 8048ba4:	55                   	push   %ebp
 8048ba5:	89 e5                	mov    %esp,%ebp
 8048ba7:	83 ec 28             	sub    $0x28,%esp                       ; 


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


 8048be9:	c9                   	leave  
 8048bea:	c3                   	ret    

08048beb <phase_3>:                                                     ; switch case
 8048beb:	55                   	push   %ebp
 8048bec:	89 e5                	mov    %esp,%ebp
 8048bee:	83 ec 38             	sub    $0x38,%esp

 
 8048bf1:	c7 45 f8 00 00 00 00 	movl   $0x0,-0x8(%ebp)             ; 

 8048bf8:	8d 45 f0             	lea    -0x10(%ebp),%eax
 8048bfb:	89 44 24 10          	mov    %eax,0x10(%esp)
 8048bff:	8d 45 ef             	lea    -0x11(%ebp),%eax
 8048c02:	89 44 24 0c          	mov    %eax,0xc(%esp)
 8048c06:	8d 45 f4             	lea    -0xc(%ebp),%eax
 8048c09:	89 44 24 08          	mov    %eax,0x8(%esp)
 8048c0d:	c7 44 24 04 dc 99 04 	movl   $0x80499dc,0x4(%esp)        ;0x80499dc:      "%d %c %d"
 8048c14:	08 
 8048c15:	8b 45 08             	mov    0x8(%ebp),%eax
 8048c18:	89 04 24             	mov    %eax,(%esp)
 8048c1b:	e8 48 fc ff ff       	call   8048868 <sscanf@plt>        ; call sscanf


 8048c20:	89 45 f8             	mov    %eax,-0x8(%ebp)             ; -0x8(%ebp) -> n
 8048c23:	83 7d f8 02          	cmpl   $0x2,-0x8(%ebp)             ; if n > 2 // 比较成功解析的字段数量是否大于2
 8048c27:	7f 05                	jg     8048c2e <phase_3+0x43>      ; 	continue
 8048c29:	e8 98 0a 00 00       	call   80496c6 <explode_bomb>


 8048c2e:	8b 45 f4             	mov    -0xc(%ebp),%eax             ; -0xc(%ebp) -> first
 8048c31:	89 45 dc             	mov    %eax,-0x24(%ebp)
 8048c34:	83 7d dc 07          	cmpl   $0x7,-0x24(%ebp)            ; if first > 7
 8048c38:	0f 87 c2 00 00 00    	ja     8048d00 <phase_3+0x115>     ; 	boom

 8048c3e:	8b 55 dc             	mov    -0x24(%ebp),%edx            ;
 8048c41:	8b 04 95 e8 99 04 08 	mov    0x80499e8(,%edx,4),%eax     ; 
 8048c48:	ff e0                	jmp    *%eax                       ; jmp Mem[0x80499e8 + 4 * (fisrt)] // 类似数组
																	   ;(gdb) x/4w 0x80499e8 U"\x8048c4a\x8048c66\x8048c82\x8048c97\x8048cac\x8048cc1\x8048cd6\x8048ceb\x25006425\x64252064"

 8048c4a:	c6 45 ff 74          	movb   $0x74,-0x1(%ebp)            ; 将字符't'移动到-0x1(%ebp)处
 8048c4e:	8b 45 f0             	mov    -0x10(%ebp),%eax			   ; -0x10(%ebp) -> third
 8048c51:	3d 66 03 00 00       	cmp    $0x366,%eax                 ; if third == 870
 8048c56:	0f 84 ad 00 00 00    	je     8048d09 <phase_3+0x11e>     ; 	continue
 8048c5c:	e8 65 0a 00 00       	call   80496c6 <explode_bomb>
 8048c61:	e9 a3 00 00 00       	jmp    8048d09 <phase_3+0x11e>


 8048c66:	c6 45 ff 61          	movb   $0x61,-0x1(%ebp)
 8048c6a:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048c6d:	3d c4 00 00 00       	cmp    $0xc4,%eax
 8048c72:	0f 84 91 00 00 00    	je     8048d09 <phase_3+0x11e>
 8048c78:	e8 49 0a 00 00       	call   80496c6 <explode_bomb>
 8048c7d:	e9 87 00 00 00       	jmp    8048d09 <phase_3+0x11e>
 8048c82:	c6 45 ff 68          	movb   $0x68,-0x1(%ebp)
 8048c86:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048c89:	3d 0d 01 00 00       	cmp    $0x10d,%eax
 8048c8e:	74 79                	je     8048d09 <phase_3+0x11e>
 8048c90:	e8 31 0a 00 00       	call   80496c6 <explode_bomb>
 8048c95:	eb 72                	jmp    8048d09 <phase_3+0x11e>
 8048c97:	c6 45 ff 6b          	movb   $0x6b,-0x1(%ebp)
 8048c9b:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048c9e:	3d 51 02 00 00       	cmp    $0x251,%eax
 8048ca3:	74 64                	je     8048d09 <phase_3+0x11e>
 8048ca5:	e8 1c 0a 00 00       	call   80496c6 <explode_bomb>
 8048caa:	eb 5d                	jmp    8048d09 <phase_3+0x11e>
 8048cac:	c6 45 ff 76          	movb   $0x76,-0x1(%ebp)
 8048cb0:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048cb3:	3d 04 01 00 00       	cmp    $0x104,%eax
 8048cb8:	74 4f                	je     8048d09 <phase_3+0x11e>
 8048cba:	e8 07 0a 00 00       	call   80496c6 <explode_bomb>
 8048cbf:	eb 48                	jmp    8048d09 <phase_3+0x11e>
 8048cc1:	c6 45 ff 66          	movb   $0x66,-0x1(%ebp)
 8048cc5:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048cc8:	3d e3 00 00 00       	cmp    $0xe3,%eax
 8048ccd:	74 3a                	je     8048d09 <phase_3+0x11e>
 8048ccf:	e8 f2 09 00 00       	call   80496c6 <explode_bomb>
 8048cd4:	eb 33                	jmp    8048d09 <phase_3+0x11e>
 8048cd6:	c6 45 ff 74          	movb   $0x74,-0x1(%ebp)
 8048cda:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048cdd:	3d 88 01 00 00       	cmp    $0x188,%eax
 8048ce2:	74 25                	je     8048d09 <phase_3+0x11e>
 8048ce4:	e8 dd 09 00 00       	call   80496c6 <explode_bomb>
 8048ce9:	eb 1e                	jmp    8048d09 <phase_3+0x11e>
 8048ceb:	c6 45 ff 6f          	movb   $0x6f,-0x1(%ebp)
 8048cef:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048cf2:	3d 11 02 00 00       	cmp    $0x211,%eax
 8048cf7:	74 10                	je     8048d09 <phase_3+0x11e>
 8048cf9:	e8 c8 09 00 00       	call   80496c6 <explode_bomb>
 8048cfe:	eb 09                	jmp    8048d09 <phase_3+0x11e>
 8048d00:	c6 45 ff 6c          	movb   $0x6c,-0x1(%ebp)
 8048d04:	e8 bd 09 00 00       	call   80496c6 <explode_bomb>


 8048d09:	0f b6 45 ef          	movzbl -0x11(%ebp),%eax           ; -0x11(%ebp) -> second
 8048d0d:	38 45 ff             	cmp    %al,-0x1(%ebp)             ; if second == 't'
 8048d10:	74 05                	je     8048d17 <phase_3+0x12c>    ; 	return true
 8048d12:	e8 af 09 00 00       	call   80496c6 <explode_bomb>
 8048d17:	c9                   	leave  
 8048d18:	c3                   	ret    

08048d19 <func4>:                                                     ; 阶乘
8048d19:	55                   	push   %ebp                       ;  
8048d1a:	89 e5                	mov    %esp,%ebp                  ;  
8048d1c:	83 ec 08             	sub    $0x8,%esp                  ; 0x8(%ebp)-> n 
8048d1f:	83 7d 08 01          	cmpl   $0x1,0x8(%ebp)             ; if n > 1
8048d23:	7f 09                	jg     8048d2e <func4+0x15>       ; 	jmp d2e
8048d25:	c7 45 fc 01 00 00 00 	movl   $0x1,-0x4(%ebp)            ; -0x4(%ebp) -> tmp = 1
8048d2c:	eb 15                	jmp    8048d43 <func4+0x2a>       ; just return 


8048d2e:	8b 45 08             	mov    0x8(%ebp),%eax             ; 
8048d31:	48                   	dec    %eax                       ; tmp = n-1
8048d32:	89 04 24             	mov    %eax,(%esp)                ; 
8048d35:	e8 df ff ff ff       	call   8048d19 <func4>            ; call func4(tmp)
8048d3a:	89 c2                	mov    %eax,%edx                  ; edx = func4(tmp)
8048d3c:	0f af 55 08          	imul   0x8(%ebp),%edx             ; output = n * func4(output)
8048d40:	89 55 fc             	mov    %edx,-0x4(%ebp)            ; return output
8048d43:	8b 45 fc             	mov    -0x4(%ebp),%eax            
8048d46:	c9                   	leave                             
8048d47:	c3                   	ret                               

08048d48 <phase_4>:                                                    ; 阶乘
8048d48:	55                   	push   %ebp                        
8048d49:	89 e5                	mov    %esp,%ebp                   
8048d4b:	83 ec 28             	sub    $0x28,%esp                  
8048d4e:	8d 45 f4             	lea    -0xc(%ebp),%eax             
8048d51:	89 44 24 08          	mov    %eax,0x8(%esp)              
8048d55:	c7 44 24 04 08 9a 04 	movl   $0x8049a08,0x4(%esp)        
08 
8048d5d:	8b 45 08             	mov    0x8(%ebp),%eax              
8048d60:	89 04 24             	mov    %eax,(%esp)                 ; 
8048d63:	e8 00 fb ff ff       	call   8048868 <sscanf@plt>        ; 调用sscanf函数，从字符串中解析数据
8048d68:	89 45 fc             	mov    %eax,-0x4(%ebp)             ; -0x4(%ebp) -> outputNum
8048d6b:	83 7d fc 01          	cmpl   $0x1,-0x4(%ebp)             ; if outputNum > 1
8048d6f:	75 07                	jne    8048d78 <phase_4+0x30>      ; 	continue
8048d71:	8b 45 f4             	mov    -0xc(%ebp),%eax             ; -0xc(%ebp) -> n
8048d74:	85 c0                	test   %eax,%eax                   ; if n > 0
8048d76:	7f 05                	jg     8048d7d <phase_4+0x35>      ; 	continue
8048d78:	e8 49 09 00 00       	call   80496c6 <explode_bomb>      ; 
8048d7d:	8b 45 f4             	mov    -0xc(%ebp),%eax             ; 
8048d80:	89 04 24             	mov    %eax,(%esp)                 ; n 入参
8048d83:	e8 91 ff ff ff       	call   8048d19 <func4>             ; call fun4

8048d88:	89 45 f8             	mov    %eax,-0x8(%ebp)             ; -0x8(%ebp) -> output
8048d8b:	81 7d f8 00 5f 37 00 	cmpl   $0x375f00,-0x8(%ebp)        ; if output == 3655424
8048d92:	74 05                	je     8048d99 <phase_4+0x51>      ;  return true
8048d94:	e8 2d 09 00 00       	call   80496c6 <explode_bomb>      ; 
8048d99:	c9                   	leave                               ; 
8048d9a:	c3                   	ret                                 ; 

08048d9b <phase_5>:                                                       ; 数组跳转
8048d9b:	55                   	push   %ebp                           ; 
8048d9c:	89 e5                	mov    %esp,%ebp                      ; 
8048d9e:	83 ec 38             	sub    $0x38,%esp                     ; 
8048da1:	8d 45 e8             	lea    -0x18(%ebp),%eax               ; 
8048da4:	89 44 24 0c          	mov    %eax,0xc(%esp)                 ; 
8048da8:	8d 45 ec             	lea    -0x14(%ebp),%eax               ; 
8048dab:	89 44 24 08          	mov    %eax,0x8(%esp)                 ; 
8048daf:	c7 44 24 04 0b 9a 04 	movl   $0x8049a0b,0x4(%esp)           ; 0x8049a0b:      "%d %d"
08 
8048db7:	8b 45 08             	mov    0x8(%ebp),%eax                 ; 
8048dba:	89 04 24             	mov    %eax,(%esp)                    ; 
8048dbd:	e8 a6 fa ff ff       	call   8048868 <sscanf@plt>           ; 调用sscanf解析输入字符串


8048dc2:	89 45 fc             	mov    %eax,-0x4(%ebp)                ; -0x4(%ebp) -> n
8048dc5:	83 7d fc 01          	cmpl   $0x1,-0x4(%ebp)                ; if n > 1
8048dc9:	7f 05                	jg     8048dd0 <phase_5+0x35>         ; 	continue
8048dcb:	e8 f6 08 00 00       	call   80496c6 <explode_bomb>         ; 
8048dd0:	8b 45 ec             	mov    -0x14(%ebp),%eax               ; -0x14(%ebp) -> first
8048dd3:	83 e0 0f             	and    $0xf,%eax                      ; 对first执行与操作，结果限制在0到15之间
8048dd6:	89 45 ec             	mov    %eax,-0x14(%ebp)               ; 
8048dd9:	8b 45 ec             	mov    -0x14(%ebp),%eax               ; 
8048ddc:	89 45 f8             	mov    %eax,-0x8(%ebp)                ; -0x8(%ebp) -> num = first
8048ddf:	c7 45 f0 00 00 00 00 	movl   $0x0,-0x10(%ebp)               ; -0x10(%ebp) -> count = 0
8048de6:	c7 45 f4 00 00 00 00 	movl   $0x0,-0xc(%ebp)                ; -0xc(%ebp) -> sum = 0


8048ded:	eb 16                	jmp    8048e05 <phase_5+0x6a>         ; 

8048def:	ff 45 f0             	incl   -0x10(%ebp)                    ; count++
8048df2:	8b 45 ec             	mov    -0x14(%ebp),%eax               ; 
8048df5:	8b 04 85 c0 a5 04 08 	mov    0x804a5c0(,%eax,4),%eax        ; Mem[0x804a5c0 + 4 * first]
8048dfc:	89 45 ec             	mov    %eax,-0x14(%ebp)               ;  // first = array[first]
8048dff:	8b 45 ec             	mov    -0x14(%ebp),%eax               ; 
8048e02:	01 45 f4             	add    %eax,-0xc(%ebp)                ; sum += first
8048e05:	8b 45 ec             	mov    -0x14(%ebp),%eax               ; 
8048e08:	83 f8 0f             	cmp    $0xf,%eax                      ; if first != 15
8048e0b:	75 e2                	jne    8048def <phase_5+0x54>         ; 	继续循环
8048e0d:	83 7d f0 0c          	cmpl   $0xc,-0x10(%ebp)               ; 	
8048e11:	75 08                	jne    8048e1b <phase_5+0x80>         ; if count == 12
8048e13:	8b 45 e8             	mov    -0x18(%ebp),%eax               ; 
8048e16:	39 45 f4             	cmp    %eax,-0xc(%ebp)                ; 	if second == sum
8048e19:	74 05                	je     8048e20 <phase_5+0x85>         ; 		return true
8048e1b:	e8 a6 08 00 00       	call   80496c6 <explode_bomb>         ; 
8048e20:	c9                   	leave                                 ; 
8048e21:	c3                   	ret                                   ;  

08048e22 <fun6>:                                                          ; 降序排列链表
 8048e22:	55                   	push   %ebp
 8048e23:	89 e5                	mov    %esp,%ebp
 8048e25:	83 ec 10             	sub    $0x10,%esp
 8048e28:	8b 45 08             	mov    0x8(%ebp),%eax
 8048e2b:	89 45 f0             	mov    %eax,-0x10(%ebp)
 8048e2e:	8b 45 08             	mov    0x8(%ebp),%eax
 8048e31:	89 45 f0             	mov    %eax,-0x10(%ebp)
 8048e34:	8b 45 08             	mov    0x8(%ebp),%eax
 8048e37:	8b 40 08             	mov    0x8(%eax),%eax
 8048e3a:	89 45 f4             	mov    %eax,-0xc(%ebp)
 8048e3d:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048e40:	c7 40 08 00 00 00 00 	movl   $0x0,0x8(%eax)
 8048e47:	eb 62                	jmp    8048eab <fun6+0x89>
 8048e49:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048e4c:	89 45 fc             	mov    %eax,-0x4(%ebp)
 8048e4f:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048e52:	89 45 f8             	mov    %eax,-0x8(%ebp)
 8048e55:	eb 0f                	jmp    8048e66 <fun6+0x44>
 8048e57:	8b 45 fc             	mov    -0x4(%ebp),%eax
 8048e5a:	89 45 f8             	mov    %eax,-0x8(%ebp)
 8048e5d:	8b 45 fc             	mov    -0x4(%ebp),%eax
 8048e60:	8b 40 08             	mov    0x8(%eax),%eax
 8048e63:	89 45 fc             	mov    %eax,-0x4(%ebp)
 8048e66:	83 7d fc 00          	cmpl   $0x0,-0x4(%ebp)
 8048e6a:	74 0e                	je     8048e7a <fun6+0x58>
 8048e6c:	8b 45 fc             	mov    -0x4(%ebp),%eax
 8048e6f:	8b 10                	mov    (%eax),%edx
 8048e71:	8b 45 f4             	mov    -0xc(%ebp),%eax
 8048e74:	8b 00                	mov    (%eax),%eax
 8048e76:	39 c2                	cmp    %eax,%edx
 8048e78:	7f dd                	jg     8048e57 <fun6+0x35>
 8048e7a:	8b 45 f8             	mov    -0x8(%ebp),%eax
 8048e7d:	3b 45 fc             	cmp    -0x4(%ebp),%eax
 8048e80:	74 0b                	je     8048e8d <fun6+0x6b>
 8048e82:	8b 55 f8             	mov    -0x8(%ebp),%edx
 8048e85:	8b 45 f4             	mov    -0xc(%ebp),%eax
 8048e88:	89 42 08             	mov    %eax,0x8(%edx)
 8048e8b:	eb 06                	jmp    8048e93 <fun6+0x71>
 8048e8d:	8b 45 f4             	mov    -0xc(%ebp),%eax
 8048e90:	89 45 f0             	mov    %eax,-0x10(%ebp)
 8048e93:	8b 45 f4             	mov    -0xc(%ebp),%eax
 8048e96:	8b 40 08             	mov    0x8(%eax),%eax
 8048e99:	89 45 f8             	mov    %eax,-0x8(%ebp)
 8048e9c:	8b 55 f4             	mov    -0xc(%ebp),%edx
 8048e9f:	8b 45 fc             	mov    -0x4(%ebp),%eax
 8048ea2:	89 42 08             	mov    %eax,0x8(%edx)
 8048ea5:	8b 45 f8             	mov    -0x8(%ebp),%eax
 8048ea8:	89 45 f4             	mov    %eax,-0xc(%ebp)
 8048eab:	83 7d f4 00          	cmpl   $0x0,-0xc(%ebp)
 8048eaf:	75 98                	jne    8048e49 <fun6+0x27>
 8048eb1:	8b 45 f0             	mov    -0x10(%ebp),%eax
 8048eb4:	c9                   	leave  
 8048eb5:	c3                   	ret    

08048eb6 <phase_6>:                                                   ; 头节点输入值，降序排列链表
 8048eb6:	55                   	push   %ebp
 8048eb7:	89 e5                	mov    %esp,%ebp
 8048eb9:	83 ec 18             	sub    $0x18,%esp
 8048ebc:	c7 45 f8 6c a6 04 08 	movl   $0x804a66c,-0x8(%ebp)     ; 0x804a66c -> addr origin, 
 																		-0x8(%ebp) -> addr start = origin
 8048ec3:	8b 45 08             	mov    0x8(%ebp),%eax
 8048ec6:	89 04 24             	mov    %eax,(%esp)
 8048ec9:	e8 8a f9 ff ff       	call   8048858 <atoi@plt>
 8048ece:	89 c2                	mov    %eax,%edx                  ; int in = atoi(input)
 8048ed0:	8b 45 f8             	mov    -0x8(%ebp),%eax
 8048ed3:	89 10                	mov    %edx,(%eax)                ; Memory[start] = in
 8048ed5:	8b 45 f8             	mov    -0x8(%ebp),%eax
 8048ed8:	89 04 24             	mov    %eax,(%esp)
 8048edb:	e8 42 ff ff ff       	call   8048e22 <fun6>             ; start = fun6(start)
 8048ee0:	89 45 f8             	mov    %eax,-0x8(%ebp)
 8048ee3:	8b 45 f8             	mov    -0x8(%ebp),%eax            ;-0x4(%ebp) -> addr n
 8048ee6:	89 45 fc             	mov    %eax,-0x4(%ebp)            ; n = start
 8048ee9:	c7 45 f4 01 00 00 00 	movl   $0x1,-0xc(%ebp)            ; -0xc(%ebp) -> int count = 1
 8048ef0:	eb 0c                	jmp    8048efe <phase_6+0x48>


 8048ef2:	8b 45 fc             	mov    -0x4(%ebp),%eax           
 8048ef5:	8b 40 08             	mov    0x8(%eax),%eax
 8048ef8:	89 45 fc             	mov    %eax,-0x4(%ebp)            ; n = Memory[n + 2]
 8048efb:	ff 45 f4             	incl   -0xc(%ebp)                 ; count++


 8048efe:	83 7d f4 08          	cmpl   $0x8,-0xc(%ebp)            ; if count <= 8
 8048f02:	7e ee                	jle    8048ef2 <phase_6+0x3c>     ; 	继续循环


 8048f04:	8b 45 fc             	mov    -0x4(%ebp),%eax
 8048f07:	8b 10                	mov    (%eax),%edx                ; Memory[n]
 8048f09:	a1 6c a6 04 08       	mov    0x804a66c,%eax             ; Memory[origin]
 8048f0e:	39 c2                	cmp    %eax,%edx                  ; if Memory[origin] == Memory[n]
 8048f10:	74 05                	je     8048f17 <phase_6+0x61>     ;  	return true
 8048f12:	e8 af 07 00 00       	call   80496c6 <explode_bomb>
 8048f17:	c9                   	leave  
 8048f18:	c3                   	ret    

08048f19 <fun7>:                                                          ; 查找二叉树节点
 8048f19:	55                   	push   %ebp                           ;
 8048f1a:	89 e5                	mov    %esp,%ebp
 8048f1c:	83 ec 0c             	sub    $0xc,%esp
 8048f1f:	83 7d 08 00          	cmpl   $0x0,0x8(%ebp)                 ; if node == 0, return
 8048f23:	75 09                	jne    8048f2e <fun7+0x15>
 8048f25:	c7 45 fc ff ff ff ff 	movl   $0xffffffff,-0x4(%ebp)
 8048f2c:	eb 54                	jmp    8048f82 <fun7+0x69>
 8048f2e:	8b 45 08             	mov    0x8(%ebp),%eax                 ;
 8048f31:	8b 00                	mov    (%eax),%eax
 8048f33:	3b 45 0c             	cmp    0xc(%ebp),%eax                 ; if input < node.val
 8048f36:	7e 1c                	jle    8048f54 <fun7+0x3b>            ;     jmp f54


 8048f38:	8b 45 08             	mov    0x8(%ebp),%eax                 ; 
 8048f3b:	8b 50 04             	mov    0x4(%eax),%edx                 ; edx = node.left
 8048f3e:	8b 45 0c             	mov    0xc(%ebp),%eax                 ; eax = input
 8048f41:	89 44 24 04          	mov    %eax,0x4(%esp)                 ; input 入参
 8048f45:	89 14 24             	mov    %edx,(%esp)                    ; node.left 入参
 8048f48:	e8 cc ff ff ff       	call   8048f19 <fun7>                 ; 递归调用 fun7
 8048f4d:	01 c0                	add    %eax,%eax                      
 8048f4f:	89 45 fc             	mov    %eax,-0x4(%ebp)                ; output += output
 8048f52:	eb 2e                	jmp    8048f82 <fun7+0x69>            ; return output

 
 8048f54:	8b 45 08             	mov    0x8(%ebp),%eax                 
 8048f57:	8b 00                	mov    (%eax),%eax
 8048f59:	3b 45 0c             	cmp    0xc(%ebp),%eax                 ; if input != node .val 
 8048f5c:	75 09                	jne    8048f67 <fun7+0x4e>            ;     jmp f67


 8048f5e:	c7 45 fc 00 00 00 00 	movl   $0x0,-0x4(%ebp)                ; output = 0
 8048f65:	eb 1b                	jmp    8048f82 <fun7+0x69>            ; return output


 8048f67:	8b 45 08             	mov    0x8(%ebp),%eax                 ; 
 8048f6a:	8b 50 08             	mov    0x8(%eax),%edx                 ; edx = node.right
 8048f6d:	8b 45 0c             	mov    0xc(%ebp),%eax
 8048f70:	89 44 24 04          	mov    %eax,0x4(%esp)                 ; input 入参
 8048f74:	89 14 24             	mov    %edx,(%esp)                    ; node.right 入参
 8048f77:	e8 9d ff ff ff       	call   8048f19 <fun7>                 ; 递归调用 fun7
 8048f7c:	01 c0                	add    %eax,%eax
 8048f7e:	40                   	inc    %eax                           ; output += output + 1
 8048f7f:	89 45 fc             	mov    %eax,-0x4(%ebp)                ; return output


 8048f82:	8b 45 fc             	mov    -0x4(%ebp),%eax                ; just return
 8048f85:	c9                   	leave  
 8048f86:	c3                   	ret      

08048f87 <secret_phase>:                                                  ; 查找值为4的节点
 8048f87:	55                   	push   %ebp
 8048f88:	89 e5                	mov    %esp,%ebp
 8048f8a:	83 ec 18             	sub    $0x18,%esp
 8048f8d:	e8 a8 03 00 00       	call   804933a <read_line>            ;read_line
 8048f92:	89 45 f4             	mov    %eax,-0xc(%ebp)
 8048f95:	8b 45 f4             	mov    -0xc(%ebp),%eax
 8048f98:	89 04 24             	mov    %eax,(%esp)
 8048f9b:	e8 b8 f8 ff ff       	call   8048858 <atoi@plt>             ;atoi
 8048fa0:	89 45 f8             	mov    %eax,-0x8(%ebp)                ; -0x8(%ebp) -> input  = atoi(read_line)
 8048fa3:	83 7d f8 00          	cmpl   $0x0,-0x8(%ebp)                ; input < 0, bomb
 8048fa7:	7e 09                	jle    8048fb2 <secret_phase+0x2b>
 8048fa9:	81 7d f8 e9 03 00 00 	cmpl   $0x3e9,-0x8(%ebp)              ; input < 39, continue
 8048fb0:	7e 05                	jle    8048fb7 <secret_phase+0x30>
 8048fb2:	e8 0f 07 00 00       	call   80496c6 <explode_bomb>
 8048fb7:	8b 45 f8             	mov    -0x8(%ebp),%eax                
 8048fba:	89 44 24 04          	mov    %eax,0x4(%esp)                 ; input 放入参数
 8048fbe:	c7 04 24 20 a7 04 08 	movl   $0x804a720,(%esp)              ; 0x804a720 <n1>: 0x24    0x804a714 <n21> 0x804a708 <n22> 0x0
 8048fc5:	e8 4f ff ff ff       	call   8048f19 <fun7>
 8048fca:	89 45 fc             	mov    %eax,-0x4(%ebp)                ;-0x4(%ebp) -> output
 8048fcd:	83 7d fc 04          	cmpl   $0x4,-0x4(%ebp)                ; if output != 4, bomb
 8048fd1:	74 05                	je     8048fd8 <secret_phase+0x51>
 8048fd3:	e8 ee 06 00 00       	call   80496c6 <explode_bomb>
 8048fd8:	c7 04 24 14 9a 04 08 	movl   $0x8049a14,(%esp)
 8048fdf:	e8 e4 f7 ff ff       	call   80487c8 <puts@plt>
 8048fe4:	e8 07 07 00 00       	call   80496f0 <phase_defused>
 8048fe9:	c9                   	leave  
 8048fea:	c3                   	ret    
 8048feb:	90                   	nop

08048fec <sig_handler>:
 8048fec:	55                   	push   %ebp
 8048fed:	89 e5                	mov    %esp,%ebp
 8048fef:	83 ec 08             	sub    $0x8,%esp
 8048ff2:	c7 04 24 8c 9c 04 08 	movl   $0x8049c8c,(%esp)
 8048ff9:	e8 ca f7 ff ff       	call   80487c8 <puts@plt>
 8048ffe:	c7 04 24 03 00 00 00 	movl   $0x3,(%esp)
 8049005:	e8 de f7 ff ff       	call   80487e8 <sleep@plt>
 804900a:	c7 04 24 c4 9c 04 08 	movl   $0x8049cc4,(%esp)
 8049011:	e8 02 f8 ff ff       	call   8048818 <printf@plt>
 8049016:	a1 80 a8 04 08       	mov    0x804a880,%eax
 804901b:	89 04 24             	mov    %eax,(%esp)
 804901e:	e8 65 f7 ff ff       	call   8048788 <fflush@plt>
 8049023:	c7 04 24 01 00 00 00 	movl   $0x1,(%esp)
 804902a:	e8 b9 f7 ff ff       	call   80487e8 <sleep@plt>
 804902f:	c7 04 24 cc 9c 04 08 	movl   $0x8049ccc,(%esp)
 8049036:	e8 8d f7 ff ff       	call   80487c8 <puts@plt>
 804903b:	c7 04 24 10 00 00 00 	movl   $0x10,(%esp)
 8049042:	e8 01 f8 ff ff       	call   8048848 <exit@plt>

08049047 <invalid_phase>:
 8049047:	55                   	push   %ebp
 8049048:	89 e5                	mov    %esp,%ebp
 804904a:	83 ec 08             	sub    $0x8,%esp
 804904d:	8b 45 08             	mov    0x8(%ebp),%eax
 8049050:	89 44 24 04          	mov    %eax,0x4(%esp)
 8049054:	c7 04 24 d4 9c 04 08 	movl   $0x8049cd4,(%esp)
 804905b:	e8 b8 f7 ff ff       	call   8048818 <printf@plt>
 8049060:	c7 04 24 08 00 00 00 	movl   $0x8,(%esp)
 8049067:	e8 dc f7 ff ff       	call   8048848 <exit@plt>

0804906c <read_six_numbers>:
 804906c:	55                   	push   %ebp
 804906d:	89 e5                	mov    %esp,%ebp
 804906f:	56                   	push   %esi
 8049070:	53                   	push   %ebx
 8049071:	83 ec 30             	sub    $0x30,%esp
 8049074:	8b 45 0c             	mov    0xc(%ebp),%eax
 8049077:	83 c0 14             	add    $0x14,%eax
 804907a:	8b 55 0c             	mov    0xc(%ebp),%edx
 804907d:	83 c2 10             	add    $0x10,%edx
 8049080:	8b 4d 0c             	mov    0xc(%ebp),%ecx
 8049083:	83 c1 0c             	add    $0xc,%ecx
 8049086:	8b 5d 0c             	mov    0xc(%ebp),%ebx
 8049089:	83 c3 08             	add    $0x8,%ebx
 804908c:	8b 75 0c             	mov    0xc(%ebp),%esi
 804908f:	83 c6 04             	add    $0x4,%esi
 8049092:	89 44 24 1c          	mov    %eax,0x1c(%esp)
 8049096:	89 54 24 18          	mov    %edx,0x18(%esp)
 804909a:	89 4c 24 14          	mov    %ecx,0x14(%esp)
 804909e:	89 5c 24 10          	mov    %ebx,0x10(%esp)
 80490a2:	89 74 24 0c          	mov    %esi,0xc(%esp)
 80490a6:	8b 45 0c             	mov    0xc(%ebp),%eax
 80490a9:	89 44 24 08          	mov    %eax,0x8(%esp)
 80490ad:	c7 44 24 04 e5 9c 04 	movl   $0x8049ce5,0x4(%esp)
 80490b4:	08 
 80490b5:	8b 45 08             	mov    0x8(%ebp),%eax
 80490b8:	89 04 24             	mov    %eax,(%esp)
 80490bb:	e8 a8 f7 ff ff       	call   8048868 <sscanf@plt>
 80490c0:	89 45 f4             	mov    %eax,-0xc(%ebp)
 80490c3:	83 7d f4 05          	cmpl   $0x5,-0xc(%ebp)
 80490c7:	7f 05                	jg     80490ce <read_six_numbers+0x62>
 80490c9:	e8 f8 05 00 00       	call   80496c6 <explode_bomb>
 80490ce:	83 c4 30             	add    $0x30,%esp
 80490d1:	5b                   	pop    %ebx
 80490d2:	5e                   	pop    %esi
 80490d3:	5d                   	pop    %ebp
 80490d4:	c3                   	ret    

080490d5 <string_length>:
 80490d5:	55                   	push   %ebp
 80490d6:	89 e5                	mov    %esp,%ebp
 80490d8:	83 ec 10             	sub    $0x10,%esp
 80490db:	8b 45 08             	mov    0x8(%ebp),%eax
 80490de:	89 45 fc             	mov    %eax,-0x4(%ebp)
 80490e1:	c7 45 f8 00 00 00 00 	movl   $0x0,-0x8(%ebp)
 80490e8:	eb 06                	jmp    80490f0 <string_length+0x1b>
 80490ea:	ff 45 fc             	incl   -0x4(%ebp)
 80490ed:	ff 45 f8             	incl   -0x8(%ebp)
 80490f0:	8b 45 fc             	mov    -0x4(%ebp),%eax
 80490f3:	0f b6 00             	movzbl (%eax),%eax
 80490f6:	84 c0                	test   %al,%al
 80490f8:	75 f0                	jne    80490ea <string_length+0x15>
 80490fa:	8b 45 f8             	mov    -0x8(%ebp),%eax
 80490fd:	c9                   	leave  
 80490fe:	c3                   	ret    

080490ff <strings_not_equal>:
 80490ff:	55                   	push   %ebp
 8049100:	89 e5                	mov    %esp,%ebp
 8049102:	53                   	push   %ebx
 8049103:	83 ec 18             	sub    $0x18,%esp
 8049106:	8b 45 08             	mov    0x8(%ebp),%eax
 8049109:	89 04 24             	mov    %eax,(%esp)
 804910c:	e8 c4 ff ff ff       	call   80490d5 <string_length>
 8049111:	89 c3                	mov    %eax,%ebx
 8049113:	8b 45 0c             	mov    0xc(%ebp),%eax
 8049116:	89 04 24             	mov    %eax,(%esp)
 8049119:	e8 b7 ff ff ff       	call   80490d5 <string_length>
 804911e:	39 c3                	cmp    %eax,%ebx
 8049120:	74 09                	je     804912b <strings_not_equal+0x2c>
 8049122:	c7 45 e8 01 00 00 00 	movl   $0x1,-0x18(%ebp)
 8049129:	eb 3e                	jmp    8049169 <strings_not_equal+0x6a>
 804912b:	8b 45 08             	mov    0x8(%ebp),%eax
 804912e:	89 45 f4             	mov    %eax,-0xc(%ebp)
 8049131:	8b 45 0c             	mov    0xc(%ebp),%eax
 8049134:	89 45 f8             	mov    %eax,-0x8(%ebp)
 8049137:	eb 1f                	jmp    8049158 <strings_not_equal+0x59>
 8049139:	8b 45 f4             	mov    -0xc(%ebp),%eax
 804913c:	0f b6 10             	movzbl (%eax),%edx
 804913f:	8b 45 f8             	mov    -0x8(%ebp),%eax
 8049142:	0f b6 00             	movzbl (%eax),%eax
 8049145:	38 c2                	cmp    %al,%dl
 8049147:	74 09                	je     8049152 <strings_not_equal+0x53>
 8049149:	c7 45 e8 01 00 00 00 	movl   $0x1,-0x18(%ebp)
 8049150:	eb 17                	jmp    8049169 <strings_not_equal+0x6a>
 8049152:	ff 45 f4             	incl   -0xc(%ebp)
 8049155:	ff 45 f8             	incl   -0x8(%ebp)
 8049158:	8b 45 f4             	mov    -0xc(%ebp),%eax
 804915b:	0f b6 00             	movzbl (%eax),%eax
 804915e:	84 c0                	test   %al,%al
 8049160:	75 d7                	jne    8049139 <strings_not_equal+0x3a>
 8049162:	c7 45 e8 00 00 00 00 	movl   $0x0,-0x18(%ebp)
 8049169:	8b 45 e8             	mov    -0x18(%ebp),%eax
 804916c:	83 c4 18             	add    $0x18,%esp
 804916f:	5b                   	pop    %ebx
 8049170:	5d                   	pop    %ebp
 8049171:	c3                   	ret    

```