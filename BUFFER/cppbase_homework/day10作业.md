第一题 

**使用嵌套类和静态对象的方式，实现单例模式的自动释放**

<span style=color:red;background:yellow>**见day10/04.AutoRelease2.cc**</span>

<img src="https://bray07.oss-cn-beijing.aliyuncs.com/undefined202403251912648.png" alt="image-20240325191259598" style="zoom: 50%;" />





第二题

在我们建立了基本的写时复制字符串类的框架后，发现了一个遗留的问题。

如果str1和str3共享一片空间存放字符串内容。如果进行读操作，那么直接进行就可以了，不用进行复制，也不用改变引用计数；如果进行写操作，那么应该让str1重新申请一片空间去进行修改，不应该改变str3的内容。

``` c++
cout << str1[0] << endl; //读操作
str1[0] = 'H';         //写操作
cout << str3[0] << endl;//发现str3的内容也被改变了
```



我们首先会想到运算符重载的方式去解决。但是str1[0]返回值是一个char类型变量。

读操作 cout << char字符 << endl;

写操作 char字符 = char字符；

无论是输出流运算符还是赋值运算符，操作数中没有自定义类型对象，无法重载。而CowString的下标访问运算符的操作数是CowString对象和size_t类型的下标，也没办法判断取出来的内容接下来要进行读操作还是写操作。

—— 提示：创建一个CowString类的内部类，让CowString的operator[]函数返回是这个新类型的对象，然后在这个新类型中对<<和=进行重载，让这两个运算符能够处理新类型对象，从而分开了处理逻辑。

<span style=color:red;background:yellow>**见day11/2.CowString2.cc**</span>

