第一题 
执行以下程序

```cpp
char *str;
cin >> str;
cout << str;
```

若输入abcd 1234，则输出（ ）

A abcd

B abcd 1234

C 1234

D 输出乱码或错误

> D,因为str没有初始化





第二题
执行以下程序

```cpp
char a[200] = {0};
cin.getline(a, 200, ' ');
cout << a;
```

若输入abcd 1234，则输出（ ）

A abcd

B abcd 1234

C 1234

D 输出乱码或错误

> A，因为设置了以空格为分隔符



第三题

**C++中的流是什么，包含哪些流？流都有哪些状态，各自的特点是什么？**

> C++ 的 I/O 发生在流中，**流是字节序列**。如果字节流是从设备（如键盘、磁盘驱动器、网络连接等）流向内存，这叫做输入操作。如果字节流是从内存流向设备（如显示屏、打印机、磁盘驱动器、网络连接等），这叫做输出操作。
>

> C++常用流类型:
>
> C++ 的输入与输出包括以下3方面的内容: 
>
> （1） 对系统指定的标准设备的输入和输出。即从键盘输入数据，输出到显示器屏幕。这种输入输出称为标准的输入输出，简称**标准** I/O 。
>
> （2） 以外存磁盘文件为对象进行输入和输出，即从磁盘文件输入数据，数据输出到磁盘文件。以外存文件为对象的输入输出称为文件的输入输出，简称**文件** I/O 。
>
> （3） 对内存中指定的空间进行输入和输出。通常指定一个字符数组作为存储空间（实际上可以利用该空间存储任何信息）。这种输入和输出称为字符串输入输出，简称**串** I/O 。

> 四种状态：
>
> - <span style=color:red;background:yellow>**badbit **</span>表示发生**系统级的错误**，如不可恢复的读写错误。通常情况下一旦 badbit 被置位，流就无法再使用了。
>
> - <span style=color:red;background:yellow>**failbit **</span>表示发生**可恢复的错误**，如期望读取一个数值，却读出一个字符等错误。这种问题通常是可以修改的，流还可以继续使用。
>
> - <span style=color:red;background:yellow>**eofbit**</span>表示**到达流结尾位置**， 此时eofbit 和 failbit 都会被置位。
>
> - <span style=color:red;background:yellow>**goodbit **</span>表示流处于**有效状态**。流在有效状态下，才能正常使用。如果 badbit 、 failbit 和 eofbit 任何一个被置位，则流无法正常使用。







第四题

**当流失效时，如何重置流的状态，并重新再正常使用流呢？**

```c++
if(cin.fail()){
   cin.clear(); //将流的状态重置为goodbit
   cin.ignore(std::numeric_limits<std::streamsize>::max(),'\n'); //清空缓冲区，以备下一次使用输入流
}
//...
```





第五题

**创建存放“存放int数据的vector”的vector，尝试进行遍历，并体会vector对象和元素的存储位置**

<span style=color:red;background:yellow>**见day07/test/vector.cc**</span>









