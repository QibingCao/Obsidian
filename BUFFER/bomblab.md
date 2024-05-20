---

类型: 笔记
创建日期: 2024-05-19
修改日期: 2024-05-19
---

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