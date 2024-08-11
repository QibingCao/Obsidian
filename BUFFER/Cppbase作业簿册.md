---
vlook-welcome: <strong>如是我闻</strong>
---

###### ~RYC™~<br>CppBase作业薄册<br>──<br><u>文档参考手册<br>(Part.I)</u><br>*最新版本`V1.0`*<br><br><br>**RoyceJen**<br>*COPYRIGHT © 2024. RoyceJen°DESIGN.*
[TOC]


# Day01 C++基础篇之类和对象01

## 第一题

`const`关键字与宏定义的区别是什么？(面试常考)

> 1. **发生的时机不同**：C语言中的宏定义发生时机在预处理时，做字符串的替换；
>
> const常量是在编译时（const常量本质还是一个变量，只是用const关键字限定之后，赋
>
> 予只读属性，使用时依然是以变量的形式去使用）
>
> 1. **类型和安全检查不同**：宏定义没有类型，不做任何类型检查；const常量有具体的类
>
> 型，在编译期会执行类型检查。

##  第二题

什么是**常量指针**和**指针常量**？什么是**数组指针**和**指针数组**？什么是**函数指针**和**指针函数**？请举例说明。

> 常量指针（const pointer）和指针常量（pointer to const）：
>
> - 常量指针是指指针本身是常量，不能指向其他变量，但可以修改指针指向的变量的值；
> - 指针常量是指指针指向的变量是常量，不能通过该指针修改变量的值，但可以改变指针指向其他变量。
>
> 数组指针（array pointer）和指针数组（pointer to array）：
>
> - 数组指针是指一个指针指向数组的首地址；
> - 指针数组是指一个数组，其中的元素是指针。
>
> 1. 函数指针（function pointer）和指针函数（pointer to function）：
>    - 函数指针是指一个指针指向函数；
>    - 指针函数是指一个函数，其返回类型是指针。

## 第三题

`new/delete`与`malloc/free`的区别是什么？(面试常考)

> **C****语言中使用**malloc**/**free**函数，**C++**使用*new**/**delete**表达式**
>
> new语句中可以不加参数，初始化为各类型默认值；也可加参数，参数代表要初始化的值

## 第四题

在自定义命名空间MySpace中定义swap函数，能够实现两个int数据的交换，并跨模块调用swap函数，验证其功能。

```cpp
#include <iostream>
#include "01.cc"
using namespace std;

int main(int argc, char * argv[]){
    int x = 5,y = 10;
    cout << "交换之前：x = " << x << ", y = "<<y<<endl;
    myspace::swap(x,y);
    cout << "交换之后: x = " << x << ", y = " << y << endl;
    cout << "hello,world" << endl;
    return 0;
}
```

```cpp
#include <iostream>
using namespace std;

namespace  myspace {
    void swap(int &a,int &b)
    {
        int temp = a;
        a = b;
        b = temp;
    }
}
```



# Day02 C++基础篇之类和对象02

## 第一题

引用与指针的区别是什么？"引用"作为函数参数有哪些特点？在什么时候需要使用"常引用"？



> 引用与指针的区别：
>
> 指针类型的只是做了一个地址传递，有复制开销
>
> 引用类型代表的就是变量本身，没有复制开销
>
> 引用作为函数参数有以下特点:
>
> 没有复制的开销，可以提高程序的执行效率
>
> 什么时候要使用常引用：
>
> 不希望函数体重通过引用改变传入的变量，那么可以使用常引用作为函数参数

## 第二题

什么是函数重载？其实现原理是什么？如何进行`C`与`C++`的混合编程？

> 函数重载是：
>
> 函数名相同，但参数列表不同：函数参数的类型、个数、顺序不同
>
> 实现原理：
>
> 名字改编（name mangling):当函数名相同时，会根据函数参数的类型、个数、顺序不同进行改编。
>
> 如何进行c与c++的混合编程：
>
> 要实现混合编程，则可以使用extern “c” {}进行解决它们之间的兼容性

## 第三题

写出下面程序的运行结果。



```cpp
#include <iostream>

using std::cout;
using std::endl;

void f2(int &x, int &y)
{
int z = x;
x = y;
y = z;
}

void f3(int *x, int *y)
{
int z = *x;
*x = *y;
*y = z;
}

int main()
{
int x, y;
x = 10; y = 26;
cout << "x, y = " << x << ", " << y << endl;
f2(x, y);
cout << "x, y = " << x << ", " << y << endl;
f3(&x, &y);
cout << "x, y = " << x << ", " << y << endl;
x++; // ++x
y--;
f2(y, x);
cout << "x, y = " << x << ", " << y << endl;
return 0;
}
```

> x, y = 10, 26
>
> x, y = 26, 10
>
> x, y = 10, 26
>
> x, y = 25, 11

## 第四题

以下代码输出的是___？

```cpp
int foo(int x,int y)
{
if(x <= 0 ||y <= 0)
return 1;
return 3 * foo(x-1, y/2);
}

cout << foo(3,5) << endl;
```

27

## 第五题

若执行下面的程序时，从键盘上输入5，则输出是（）

```cpp
int main(int argc, char** argv)
{
int x;
cin >> x;
if(x++ > 5)
{
cout << x << endl;
}
else
{
cout << x-- << endl;
}

return 0;
}
```

输出是6，但x此时的值为5

## 第六题

写出下面程序的结果：

```cpp
int main()
{
int a[5]={1,2,3,4,5};
int *ptr=(int *)(&a+1);
printf("%d,%d",*(a+1),*(ptr-1));
}
```

2，5

# Day03 C++基础篇之类和对象01

## 第一题

**C++内存布局是怎样的？可以具体阐述一下么？**

<img src="http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240307221230520.png" alt="image-20240307221230520" style="zoom: 67%;" />

C++ 内存布局通常包括以下几个部分：

1. **代码区（Text Segment）**： 代码区存储程序的机器代码，也就是编译后的可执行指令。这部分内存通常是只读的，并且是共享的，即相同程序的不同进程可以共享同一个代码区。
2. **全局数据区（Global Data Segment）**： 全局数据区存储全局变量和静态变量，它在程序的整个生命周期内都存在。全局变量是在程序运行之初就分配内存空间的，直到程序结束才释放。静态变量的生命周期与全局变量相同，但它们可能只在声明它们的文件中可见。
3. **堆区（Heap Segment）**： 堆区是动态分配内存的地方，也就是通过 `new`、`malloc` 等运算符或函数分配的内存。堆区的大小是动态变化的，程序员负责手动管理这部分内存的分配和释放。如果不释放已分配的内存，就会发生内存泄漏。
4. **栈区（Stack Segment）**： 栈区用来存放函数的局部变量、函数参数以及函数调用时的返回地址。每次函数调用时，会在栈上分配一块新的内存空间，函数执行结束时会自动释放这部分内存。栈区的大小是有限的，通常由操作系统预先分配好，不需要程序员手动管理。
5. **常量区/静态存储区（Constant/Static Storage Segment）**： 常量区存储程序中的字符串常量和其他常量数据，例如 `const` 修饰的全局变量和静态变量。这部分内存通常是只读的。

## 第二题

**什么是拷贝构造函数，其形态是什么，参数可以修改吗？**

拷贝构造函数：

该函数是一个构造函数，用一个已经存在的同类型的对象，来初始化新对象，即对对象本身进行复制。

形态：

类名（const 类名 &）

其参数不可以修改，&是引用意义，不能去掉引用符号，也不能去掉const关键字

## 第三题

**什么情况下，会调用拷贝构造函数?**

拷贝构造函数的调用时机：

1. 当用一个已经存在的对象初始化另一个对象时
2. 当函数参数是对象，形参与实参结合时（实参初始化形参）
3. 当函数的返回值是对象，执行return语句时

## 第四题

设已经有A,B,C,D 4个类的定义，程序中A,B,C,D析构函数调用顺序为？

```cpp
C c;
void main()
{
    A *pa=new A();
    B b;
    static D d;
    delete pa;
}
```

程序中A,B,C,D析构函数调用顺序为：

A-->堆对象销毁

B-->栈对象销毁

C-->全局对象，在main函数退出时销毁

D-->局部静态对象，在main函数退出时销毁

## 第五题

定义一个学生类，其中有3个数据成员：学号、姓名、年龄，以及若干成员函数。

同时编写main函数，使用这个类，实现对学生数据的赋值和输出。

```cpp
#include<cstring>
#include <iostream>
using namespace std;

class student
{
public:
    void setId(int id)
    {
        _stuid = id;
    }

    void setName(char *name)
    {
        strcpy(_name,name);
    }

    void setAge(int age)
    {
        _age = age;
    }

    void print()
    {
        cout << "Id:" << _stuid << endl
            << "name:" << _name <<endl
            << "age:"<<_age << endl;
    }
private:
    int _stuid;
    char _name[20];
    int _age;
};

void test()
{
    student s1;
    int id = 21;
    char name[] = "ryc";
    int age = 22;
    s1.setId(id);
    s1.setName(name);
    s1.setAge(age);
    s1.print();

}


int main(int argc, char * argv[]){
    test();
    return 0;
}
```

![image-20240307224609728](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240307224609728.png)

# Day04 C++基础篇之类和对象01

## 第一题

什么是赋值运算符函数，其形态是什么？什么情况下需要手动提供赋值运算符函数呢？

> 什么是赋值运算符函数：
>
> 赋值运算符函数是用于对自定义类型的对象进行赋值操作，允许对象之间进行成员变量的赋值，以及对动态分配的资源进行适当的管理。
>
> 形态：
>
> 类名& operator=（const 类名 &对象名）
>
> 需要手动提供赋值运算符函数的情况包括：
>
> 1. 类中包含指针成员变量，且这些指针指向动态分配的资源（如内存、文件句柄等）时，需要手动管理资源的释放和复制。
> 2. 类中包含非指针成员变量，并且需要自定义的赋值行为，而不是简单的逐个成员变量的赋值。
> 3. 类中包含了资源管理类对象（例如std::vector、std::string等），这些类对象在进行赋值操作时需要进行深度拷贝，以避免资源的重复释放或内存泄漏。
>
> 在这些情况下，手动提供赋值运算符函数可以确保正确地管理资源，避免内存泄漏或资源重复释放等问题。





## 第二题

this指针是什么? 有什么作用呢？

> this指针：
>
> this指针的本质是一个指针常量Type* const pointer;它储存了调用它的对象的地址，不可被修改。这样的成员函数才知道自己修改的成员变量是那个对象的。
>
> this是一个隐藏的指针，可以在类的成员函数中使用，它可以用来指向调用对象。当一个对象的成员函数被调用时，编译器会隐式地传递该对象的地址作为this
>
> 指针。this指针指向本对象。

## 第三题

写出以下程序运行的结果。

```cpp
class MyClass
{
public:
    MyClass(int i = 0)
    {
        cout << i;
    }
    MyClass(const MyClass &x)
    {
        cout << 2;
    }
    MyClass &operator=(const MyClass &x)
    {
        cout << 3;
        return *this;
    }
    ~MyClass()
    {
        cout << 4;
    }
};
int main()
{
    MyClass obj1(1), obj2(2);
    MyClass obj3 = obj1;
    return 0;
}
```

输出的是：12444

12表示创建对象传入的参数，三个4代表三个析构函数执行的输出

![image-20240308203120324](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240308203120324.png)

## 第四题



不考虑任何编译器优化(如:NRVO),下述代码的第10#会发生

`-fno-elide-constructors` 编译时消除优化的影响

```cpp
classB
{
public:
	B()
	{
        cout << "B()" << endl;
    }

    ~B()
    {
    	cout << "~B()" << endl;
    }
    
    B(const B &rhs)
    {
        cout << "B(const B&)" << endl;
    }
    
    B &operator=(const B &rhs)
    {
    	cout << "B &operator=(const B &s)" << endl;
    
        return  *this;
    }
};

B func(const B &rhs)
{
    cout << "B func(const B &)" << endl;
    return rhs;
}


int main(int argc, char **argv)
{
	B b1,b2;
    b2=func(b1);//10#

	return 0;
}
```

> 创建了两个对象b1和b2,调用两次构造函数
>
> 调用func功能函数，传参是引用，b1作为参数传入，输出：B func(const B &)
>
> 在func功能函数内部，函数func要返回b2的副本，这会触发一次拷贝构造函数调用
>
> 其返回值被赋给b2，调用赋值运算符函数
>
> main函数结束，三个对象的生命周期结束，b1,b2调用析构函数，func函数返回值返回一个临时的B对象，这个对象的生命周期与等号右边的表达式结束 `b2=func(b1);` ，这个临时对象的析构函数 `~B()` 会在这个表达式结束时被调用。

![image-20240308205700138](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240308205700138.png)

## 第五题



实现上课时候的单例模式的代码，试一试补充功能，能够修改堆上单例对象的内容。

```cpp
#include <iostream>
using namespace std;


//单例模式-->一个类只能生成一个对象，且是唯一的对象
//1. 将构造函数私有化
//2. 在类中定义一个静态的指向本类型的指针变量
//3. 定义一个返回值为类指针的静态成员函数
class Singleton
{
public:
    static Singleton * getInstance()
    {
        if(nullptr == _pInstance){
            _pInstance = new Singleton(0);
        }
        return _pInstance;
    }

    static void release()
    {
        if(_pInstance){
            delete _pInstance;
        }
    }

    void setNum(int num)
    {
        this->_num = num;
    }

    int getNum()
    {
        return _num;
    }
private:
    //只能在类内部调用
    //构造函数
    Singleton(int num)
    :_num(num)
    {   
        cout << "Singleton( int  )" << endl;  
    }
    
    //析构函数
    ~Singleton()    {   cout << "~Singleton()" << endl; }
    
    int _num;
    static Singleton * _pInstance;
};

Singleton * Singleton::_pInstance = nullptr;
void test()
{
    Singleton *p1 = Singleton::getInstance();
    Singleton *p2 = Singleton::getInstance();
    cout << "p1:" << p1 << endl;
    cout << "p2:" << p2 << endl;
    
    cout << "getNum = " << p1->getNum() <<endl;
    int a= 10;
    p2->setNum(a);
    cout << "setNum(10) = " << p1->getNum() <<endl;
    
    Singleton::release();

}

int main(int argc, char * argv[]){
    test();
    return 0;
}
```

![image-20240308222346568](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240308222346568.png)

# Day 05 C++基础篇之类和对象01

## 第一题

什么是C风格的字符串？什么是C++风格的字符串？说说你熟悉的C++风格字符串的常用操作，各操作的功能是什么？

在C语言中，字符串通常被表示为以null字符（'\0'）结尾的字符数组。这种表示方法称为C风格的字符串。

在C++中，可以使用标准库中的`std::string`类来表示和处理字符串，这种方式称为C++风格的字符串。

常用操作：string对象常用的构造、

## 第二题

实现一个类只能生成堆对象，不能生成栈对象的代码，并能做到正常回收该堆对象。

```cpp
#include<cstring>
#include <iostream>
using namespace std;

class Student
{
public:
    Student(int id,const char * name)
    :_id(id)
    ,_name(new char[strlen(name) + 1]())
    {
        cout << "Student(id ,name) " <<endl;
        strcpy(_name,name);
    }
public:
    void print() const
    {
        cout << "id:" << _id << endl
            << "name:" <<_name << endl;
    }

    void *operator new(size_t sz)
    {
        cout << "void * operator new(size_t)" << endl;
        return malloc(sz);
    }

    void operator delete(void * pointer)
    {
        cout << "void  operator delete(void*)" << endl;
        free(pointer);
    }

    void release()
    {
        delete this;
    }
private:
    ~Student()
    {
        if(_name)
        {
            delete [] _name;
            _name = nullptr;
        }
        cout << "~Student()" << endl;
    }
    int _id;
    char * _name;

};

void test()
{
    Student * pstu = new Student(21,"ryc");
    pstu->print();
    pstu->release();
}

int main(int argc, char * argv[]){
    test();
    return 0;
}
```



## 第三题

实现一个自定义的String类，保证main函数对正确执行。实现时，记得采用增量编写的方式，逐一测试。

```cpp
class String
{
public:
	String();
	String(const char *pstr);
	String(const String &rhs);
	String &operator=(const String &rhs);
	~String();
	void print();
    size_t length() const;
    const char * c_str() const;

private:
	char * _pstr;
};

int main()
{
	String str1;
	str1.print();
	

	String str2 = "Hello,world";
	String str3("wangdao");
	
	str2.print();		
	str3.print();	
	
	String str4 = str3;
	str4.print();
	
	str4 = str2;
	str4.print();
	
	return 0;
}
```

```cpp
#include<cstring>
#include <iostream>
using namespace std;

class String
{
public:
        String();
        String(const char *pstr);
        String(const String &rhs);
        String &operator=(const String &rhs);
        ~String();
        void print();
    size_t length() const;
    const char * c_str() const;

private:
        char * _pstr;
};

//构造函数
String::String()    { cout << "String()构造" << endl; }

//析构函数
String::~String() {cout << "~String()析构 " << endl; }

//通过C风格字符串构一个string对象
String::String(const char *pstr)
{
    cout << "String(const char *pstr)" << endl;
    if(pstr)
    {
        _pstr = new char[strlen(pstr) + 1];
        strcpy(_pstr,pstr);
    }
    else
    {
        _pstr = new char[1];
        *_pstr = '\0';
    }
}

//拷贝构造函数
String::String(const String &rhs)
{
    cout << "String(const String &rhs)" << endl;
    _pstr = new char[strlen(rhs._pstr) + 1];
    strcpy(_pstr,rhs._pstr);
}

//赋值运算符重载
String &String::operator= (const String &rhs)
{
    cout << "operator=" << endl;
    if(this != &rhs)
    {
        delete  [] _pstr;
        _pstr = new char[strlen(rhs._pstr) + 1];
        strcpy(_pstr,rhs._pstr);
    }
    return *this;
}

//打印函数
void String::print()
{
    cout << (_pstr ? _pstr :"nullptr") << endl;
}

//返回字符串长度
size_t String::length() const
{
    cout << "length" << endl;
    return (_pstr ? strlen(_pstr) : 0);
}

//返回C风格字符串
const char *String::c_str()const
{
    cout << "String::Cstr" << endl;
    return _pstr ? _pstr : "";
}

int main()
{
        String str1;
        str1.print();


        String str2 = "Hello,world";
        String str3("wangdao");

        str2.print();
        str3.print();

        String str4 = str3;
        str4.print();

        str4 = str2;
        str4.print();

    // 测试length和c_str成员函数
    String str("Hello");
    cout << "Length of str: " << str.length() 
        << endl; // 期望输出: 5
    cout << "C-style string of str: " << 
        str.c_str() << endl; // 期望输出: Hello

        return 0;
}
```



## 第四题

编写一个类，实现简单的栈。栈中有以下操作，并自行写出测试用例，每个函数都需要测试到。

```cpp
class Stack {
public:
	bool empty();	//判断栈是否为空
	bool full();	//判断栈是否已满
	void push(int); //元素入栈
	void pop();     //元素出栈
	int  top();		//获取栈顶元素
//...
};
```

```cpp
#include<vector>
#include <iostream>
using namespace std;

class Stack {
public:
        bool empty() const;     //判断栈是否为空
        bool full() const;      //判断栈是否已满
        void push(int value); //元素入栈
        void pop();     //元素出栈
        int  top() const;               //获取栈顶元素

private:
    std::vector<int> data;  // 使用vector作为底层容器存储栈元素
    static const int MAX_SIZE = 100; // 栈的最大容量
};

//判断栈是否为空
bool Stack::empty() const
{
    return data.empty();
}

//判断栈是否已满
bool Stack::full() const
{
    return data.size() >= MAX_SIZE;
}

//入栈
void Stack::push(int value)
{
    if(full())
    {
        cerr << "stack is full" << endl;
        return;
    }
    data.push_back(value);
}

//出栈
void Stack::pop() {
    if (empty()) {
        cerr << "Stack is empty!" << endl;
        return;
    }
    data.pop_back();
}

//获取栈顶元素
int Stack::top() const {
    if (empty()) {
        cerr << "Stack is empty!" << endl;
        return -1; // 返回一个默认值表示栈为空
    }
    return data.back();
}

//测试
void test() {
    // 创建一个栈对象
    Stack stack;

    // 测试empty函数
    if (stack.empty())
        cout << "Stack is empty" << endl;
    else
        cout << "Stack is not empty" << endl;

    // 测试push函数
    for (int i = 1; i <= 120; ++i) {
        stack.push(i);
    }

    //判满
    if(stack.full())
    {
        cout << "stack is full" << endl;
    }
    else
    {
        cout << "stack is not full" << endl;
    }

    //测试栈顶元素
    int ret = stack.top();
    cout << "top = " << ret <<endl;
    //出栈
    for(int i = 0;i <= 100;i++)
    {
        stack.pop();
    }

}

int main(int argc, char * argv[]){
    cout << "hello,world" << endl;
    test();
    return 0;
}
```

# Day06 C++基础篇之类和对象01

## 第一题

执行以下程序

```cpp
char *str;
cin >> str;
cout << str;
```

若输入abcd 1234，则输出（ ）

- A	abcd
- B    abcd 1234
- C     1234
- <span style=color:red;background:yellow>**D     输出乱码或错误**</span>

## 第二题

执行以下程序

```cpp
char a[200] = {0};
cin.getline(a, 200, ' ');
cout << a;
```

若输入abcd 1234，则输出（ ）

- <span style=color:red;background:yellow>**A	abcd**</span>
- B    abcd 1234
- C     1234
- D     输出乱码或错误

## 第三题

C++中的流是什么，包含哪些流？流都有哪些状态，各自的特点是什么？

> C++中的流：流，就是流动，事物从一处流向另一处的过程；流中的基本单位是字节，因此称为字节流；
>
> 流都有四种状态：
>
> goodbit表示流处于有效状态，只有流有效时，才能正常使用
>
> badbit表示发生系统级别的错误，无法恢复
>
> failbit表示发生了可以恢复的错误
>
> eofbit表示到达流结尾位置，此时eofbit和failbit都会被置位
>

## 第四题

当流失效时，如何重置流的状态，并重新再正常使用流呢？

> 当流失效时：
>
> 可以i调用clear方法，进行重置再利用ignore函数进行清空缓冲区
>

## 第五题

创建存放“存放int数据的vector”的vector，尝试进行遍历，并体会vector对象和元素的存储位置

```cpp
#include<vector>
#include <iostream>
using namespace std;

void test0()
{
    int arr[10] = {1,2,3,4,5,6,7,8,9,10};
    vector<int> numbers(arr,arr+10);
    cout << "the address of numbers :" << &numbers <<endl;

    for(size_t i = 0; i < numbers.size();++i)
    {
        cout << "i = " << i <<"number[i] = " << numbers[i] <<endl;
        cout << "&numbers[i] = " << &numbers[i] << endl;
    }

}

int main(int argc, char * argv[]){
    test0();
    return 0;

}
```

![image-20240311210135446](C:\Users\任永昌\AppData\Roaming\Typora\typora-user-images\image-20240311210135446.png)

由测试结果可知，vector对象numbers在栈上分配内存，是栈区的高地址。

而numbers里面的元素，是在堆上分配内存的，vector使用动态内存分配来存储其元素。

# Day07 C++基础篇之类和对象01

## 第一题

什么是友元？友元的存在形式有？友元有何特点？

> <span style=color:red;background:yellow>**什么是友元：**</span>
>
> ​		在C++中，友元（Friend）是一种机制，允许一个类或者函数访问另一个类的私有成员。通常情况下，私有成员只能被声明它们的类的成员函数访问，但是有时候我们需要在其他类或者函数中访问这些私有成员，这时就可以使用友元。
>
> <span style=color:red;background:yellow>**友元的存在形式：**</span>
>
> 三种形式：普通函数、成员函数、友元类
>
> <span style=color:red;background:yellow>**特点：**</span>
>
> 1. 友元不受类中访问权限的限制——可访问私有成员
>
> 2. 友元破坏了类的封装性
> 3. 不能滥用友元 ，友元的使用受到限制
> 4. 友元是单向的——A类是B类的友元类，则A类成员函数中可以访问B类私有成员；但并
> 不代表B类是A类的友元类，如果A类中没有声明B类为友元类，此时B类的成员函数中并
> 不能访问A类私有成员
> 5. 友元不具备传递性——A是B的友元类，B是C的友元类，无法推断出A是C的友元类
> 6. 友元不能被继承——因为友元破坏了类的封装性，为了降低影响，设计层面上友元不能
> 被继承

## 第二题

**自增运算符**的前置形式和后置形式有什么区别？返回值类型分别是什么？哪一种形式的效率更高呢？

1. **前置形式：** `++variable`，在变量前面使用自增运算符。它会先将变量的值加一，然后返回增加后的值，**即直接返回variable变量本身**。
2. **后置形式：** `variable++`，在变量后面使用自增运算符。它会先返回变量的当前值（返回的值是一个临时变量-->variable的值改变前的副本），然后再将变量的值加一。

效率上，前置形式和后置形式没有太大的差异，通常情况下编译器会对其进行优化，因此它们的性能几乎是相同的。不过，如果不需要使用原始值，建议使用前置形式，因为它只需要一次自增操作，而后置形式需要在返回之前保存原始值，然后再执行自增操作，因此略微慢一些。

## 第三题

编写Base类使下列代码输出为1

```cpp
int i=2;
int j=7;

Base x(i);
Base y(j);
cout << (x+y == j - i) << endl;
```

提示：本题考查的其实就是运算符重载的知识点。

```cpp
#include <iostream>
using namespace std;

class Base
{
public:
    Base (int x)
    :_x(x)
    {}
    void print() const
    {
        cout << _x <<endl;
    }
    friend 
        Base operator+(const Base & lhs,const Base & rhs);
    friend
        bool operator==(const Base & lhs,int rhs);
private:
    int _x;
};

Base operator+(const Base & lhs,const Base & rhs)
{
    return Base(rhs._x - lhs._x);
}

bool operator==(const Base& lhs, int rhs) { 
    return (lhs._x == rhs);
}

void test0()
{
    int i = 2;
    int j = 7;

    Base x(i);
    Base y(j);
    cout << (x+y == j - i) << endl;
}

int main(int argc, char * argv[]){
    test0();
    return 0;

}
```

## 四题

使用log4cpp格式化输出的信息，同时要求输出到终端、文件和回卷文件中。

提示：通过实践来感受log4cpp的使用，这是学习任何第三方库的第一步要做的事儿，先从模仿开始，再慢慢逐步理解。学会使用第三方库，是工作中的基本能力，一定要掌握方法论。



## 第五题

用所学过的类和对象的知识，封装log4cpp，让其使用起来更方便，要求：可以像printf一样 ，同时输出的日志信息中最好能有文件的名字，函数的名字及其所在的行号(这个在C/C++里面有对应的宏，可以查一下)



代码模板：

```cpp
class Mylogger
{
public:
	void warn(const char *msg);
	void error(const char *msg);
	void debug(const char *msg);
	void info(const char *msg);
	
private:
	Mylogger();
	~Mylogger();
    
private:
  //......
};


void test0()
{
    //第一步，完成单例模式的写法
    Mylogger *log = Mylogger::getInstance();

    log->info("The log is info message");
    log->error("The log is error message");
    log->fatal("The log is fatal message");
    log->crit("The log is crit message");
}

void test1() 
{
    printf("hello,world\n");
    //第二步，像使用printf一样
    //只要求能输出纯字符串信息即可，不需要做到格式化输出
    LogInfo("The log is info message");
    LogError("The log is error message");
    LogWarn("The log is warn message");
    LogDebug("The log is debug message");
}
```

mylogger.hpp

```cpp

#ifndef _MYLOGGER_HPP_
#define _MYLOGGER_HPP_

#include<log4cpp/Category.hh>
using namespace log4cpp;

class Mylogger 
{
public:
    static Mylogger *getInstance();
    static void destory();

    void warn(const char * msg);
    void error(const char * msg);
    void debug(const char * msg);
    void info(const char * msg);
private:
    Mylogger();
    ~Mylogger();

private:
    static Mylogger *_pInstance;
    Category & _mycat;
};

#define prefix(msg) string(__FILE__).append("  ").append(__FUNCTION__).\
    append(string(" ")).append(string(std::to_string(__LINE__))).append(":").\
     append(msg).c_str()

#define logError(msg)  Mylogger::getInstance()->error(prefix(msg));
#define logWarn(msg)  Mylogger::getInstance()->warn(prefix(msg));
#define logDebug(msg)  Mylogger::getInstance()->debug(prefix(msg));
#define logInfo(msg)  Mylogger::getInstance()->info(prefix(msg));

#endif
```

mylogger.cc

```CPP
#include "mylogger.hpp"
#include <iostream>
#include <log4cpp/OstreamAppender.hh>
#include <log4cpp/FileAppender.hh>
#include <log4cpp/PatternLayout.hh>
#include <log4cpp/Priority.hh>

using namespace std;
using namespace log4cpp;
Mylogger *Mylogger::_pInstance = nullptr;

Mylogger *Mylogger::getInstance()
{
    if(nullptr == _pInstance)
    {
        _pInstance = new Mylogger();
    }
    return _pInstance;
}

void Mylogger::destory()
{
    if(_pInstance)
    {
        delete _pInstance;
        _pInstance = nullptr;
    }
}

void Mylogger::warn(const char * msg)
{
    _mycat.warn(msg);
}

void Mylogger::error(const char * msg)
{
    _mycat.error(msg);
}

void Mylogger::debug(const char * msg)
{
    _mycat.debug(msg);
}

void Mylogger::info(const char * msg)
{
    _mycat.info(msg);
}

Mylogger::Mylogger()
: _mycat(Category::getRoot().getInstance("Mycat"))
{
    cout << "Mylogger()" << endl;

    //日志的格式
    PatternLayout *ppl1 = new PatternLayout();
    ppl1->setConversionPattern("%d %c [%p] %m%n");

    PatternLayout *ppl2  = new PatternLayout();
    ppl2->setConversionPattern("%d %c [%p] %m%n");

    //日志目的地
    OstreamAppender *pos = new OstreamAppender("OstreamAppender122",&cout);
    pos->setLayout(ppl1);

    FileAppender *pfa = new FileAppender("FileAppender12","wd.log");
    pfa->setLayout(ppl2);

    _mycat.addAppender(pos);
    _mycat.addAppender(pfa);

    //日志的优先级
    _mycat.setPriority(Priority::DEBUG);

}

Mylogger::~Mylogger()
{
    cout << "~Mylogger()" << endl;
    Category::shutdown();
}
```

testmylogger.cc

```cpp
#include "mylogger.hpp"

#include <iostream>
#include<string>

using namespace std;

void test0()
{
    //第一步，完成单例模式的写法
    //从C++风格字符串转换为C风格字符串 c_str()
    Mylogger *p1 = Mylogger::getInstance();
    p1->warn("hello,bitch");
    Mylogger::getInstance()->error("This is an error message");
    //Mylogger::getInstance()->error(func("This is an error message").c_str());
    Mylogger::getInstance()->error(prefix("This is an error message"));
    Mylogger::getInstance()->warn(prefix("This is an error message"));
    Mylogger::getInstance()->info(prefix("This is an error message"));
    Mylogger::getInstance()->debug(prefix("This is an error message"));
    logError("This is an error message");
    //printf();
}
void test1()
{
    //printf("hello,world\n");
    //第二步，像使用printf一样
    //只要求能输出纯字符串信息即可，不需要做到格式化输出
    logInfo("The log is info message");
    logError("The log is error message");
    logWarn("The log is warn message");
    logDebug("The log is debug message");
}

int main()
{
    cout << "hello" <<endl <<endl;
    test0();
    test1();
    return 0;
}
```



# Day08

无

# Day09

## 第一题

C++中函数对象、成员函数指针该如何使用呢？请用代码给出示例。



## 第二题

实现以下String类的各个函数，并给出相应的测试用例

```cpp
class String 
{
public:
	String();
	String(const char *);
	String(const String &);
	~String();
	String &operator=(const String &);
	String &operator=(const char *);

	String &operator+=(const String &);
	String &operator+=(const char *);
	
	char &operator[](std::size_t index);
	const char &operator[](std::size_t index) const;
	
	std::size_t size() const;
	const char* c_str() const;
	
	friend bool operator==(const String &, const String &);
	friend bool operator!=(const String &, const String &);
	
	friend bool operator<(const String &, const String &);
	friend bool operator>(const String &, const String &);
	friend bool operator<=(const String &, const String &);
	friend bool operator>=(const String &, const String &);
	
	friend std::ostream &operator<<(std::ostream &os, const String &s);
	friend std::istream &operator>>(std::istream &is, String &s);

private:
	char * _pstr;
};

String operator+(const String &, const String &);
String operator+(const String &, const char *);
String operator+(const char *, const String &);
```

```cpp
#include <iostream>
#include<string.h>
#include<vector>
using namespace std;

class String 
{
public:
        String()
    : _pstr(new char[1]())
    {
        cout << "string()" << endl;
    }

    //String si("hello")
    //String s1 = "hello";/String("hello")
    //"hello" ===== String("hello")
        String(const char *pstr)
    : _pstr(new char[strlen(pstr) + 1])
    {
        cout << "string(const char *pstr)" << endl;
        strcpy(_pstr,pstr);
    }

    //String s1(s2);
    //String s2 = s1;
        String(const String &rhs)
    :_pstr(new char[strlen(rhs._pstr) + 1]())
    {
        cout << "String(const String &rhs)" << endl;
        strcpy(_pstr,rhs._pstr);
    }

    //析构函数
        ~String()
    {
        cout << "~String()" << endl;
        if(_pstr)
        {
            delete [] _pstr;
            _pstr = nullptr;
        }
    }

    //String s1;
    //s1 = s1;
        String &operator=(const String &rhs)
    {
        cout << "String &operator=(const String &)" <<endl;
        if(this != &rhs)
        {
            delete [] _pstr;
            _pstr = nullptr;

            _pstr = new char[strlen(rhs._pstr) + 1]();
            strcpy(_pstr,rhs._pstr);
        }
        return *this;
    }

    //s1 = "hello"
        String &operator=(const char *pstr)
    {
        cout << "String &operator=(const char *pstr)" << endl;
        String temp(pstr);
        *this = temp;
        return *this;
    }

    //s1 += s2;
        String &operator+=(const String &rhs)
    {
        cout << "String &operator+=(const String &)" <<endl;
        String temp;
        if(temp._pstr)
        {
            delete [] temp._pstr;//防止内存泄漏
        }

        temp._pstr = new char[strlen(_pstr) + 1]();
        strcpy(temp._pstr,_pstr);
        delete [] _pstr;
        _pstr = nullptr;
        _pstr = new char[strlen(rhs._pstr) + strlen(temp._pstr) +1]();
        strcpy(_pstr,temp._pstr);
        strcat(_pstr,rhs._pstr);

        return *this;
    }

    //s1 += "hello";
        String &operator+=(const char *pstr)
    {
        cout << "String &operator+=(const char *pstr)" << endl;
        String temp(pstr);
        *this += temp;
        return *this;
    }

    //const String s1("hello");
    //s1[0]
        char &operator[](std::size_t index)//size_t-->index >= 0
    {
        if(index < size())
        {
            return _pstr[index];
        }
        else
        {
            static char nullchar = '\0';
            return nullchar;
        }
    }

        const char &operator[](std::size_t index) const
    {
        if(index < size())
        {
            return _pstr[index];
        }
        else
        {
            static char nullchar = '\0';
            return nullchar;
        }
    }

    //size()
        std::size_t size() const
    {
        return strlen(_pstr);
    }

        const char* c_str() const
    {
        return _pstr;
    }

        friend bool operator==(const String &, const String &);
        friend bool operator!=(const String &, const String &);

        friend bool operator<(const String &, const String &);
        friend bool operator>(const String &, const String &);
        friend bool operator<=(const String &, const String &);
        friend bool operator>=(const String &, const String &);

        friend std::ostream &operator<<(std::ostream &os, const String &s);
        friend std::istream &operator>>(std::istream &is, String &s);

private:
        char * _pstr;
};

bool operator==(const String &lhs, const String &rhs)
{
    return !strcmp(lhs._pstr, rhs._pstr);
}

bool operator!=(const String &lhs, const String &rhs)
{
    return strcmp(lhs._pstr, rhs._pstr);
}

bool operator<(const String &lhs, const String &rhs)
{
    return strcmp(lhs._pstr, rhs._pstr) < 0;
}

bool operator>(const String &lhs, const String &rhs)
{
    return strcmp(lhs._pstr, rhs._pstr) > 0;
}
bool operator<=(const String &lhs, const String &rhs)
{
    return strcmp(lhs._pstr, rhs._pstr) <= 0;
}
bool operator>=(const String &lhs, const String &rhs)
{
    return strcmp(lhs._pstr, rhs._pstr) >= 0;
}

std::ostream &operator<<(std::ostream &os, const String &rhs)
{
    if(rhs._pstr)
    {
        os << rhs._pstr;
    }

    return os;
}

//String s1("hello")
//cin >> s1;
std::istream &operator>>(std::istream &is, String &rhs)
{
    if(rhs._pstr)
    {
        delete [] rhs._pstr;
        rhs._pstr = nullptr;
    }

    //动态获取从键盘输入数据的长度
    vector<char> buffer;
    char ch;
    while((ch = is.get()) != '\n')
    {
        buffer.push_back(ch);
    }

    rhs._pstr = new char[buffer.size() + 1]();
    strncpy(rhs._pstr, &buffer[0], buffer.size());

    return is;
}

String operator+(const String &lhs, const String &rhs)
{
    cout << "String operator+(const String &, const String &)" << endl;

    String tmp(lhs);
    tmp += rhs;

    return tmp;
}
//s1 + "hello"
String operator+(const String &lhs, const char *pstr)
{
    cout << "String operator+(const String &, const char *)"<< endl;
    String tmp(lhs);
    tmp += pstr;

    return tmp;

}

//"hello" + s1
String operator+(const char *pstr, const String &rhs)
{
    cout << "String operator+(const char*, const String &)" << endl;
    String tmp(pstr);
    tmp += rhs;

    return tmp;
}


void test()
{
    String s1;
    std::cin >> s1;
    cout << "s1 = " << s1 << endl;

    cout << endl << endl;
    String s2 = "hello";
    cout << "s2 = " << s2 << endl;

    cout << endl << "1111" <<  endl;
    s2 = "world"; //error
    cout << "s2 = " << s2 << endl;

    cout << endl << endl;
    s2 = s2;
    cout << "s2 = " << s2 << endl;

    cout << endl << endl;
    String s3 = "wuhan";
    s3 += " welcome to string word";
    cout << "s3 = " << s3 << endl;
}

void test1(){
    int * pint = nullptr;
    cout << "pint:" << pint << endl;
    cout << "over" << endl;

    char * pstr = nullptr;
    cout << "pstr:" << pstr << endl;
    cout << "over" << endl;
    cout << "*pstr = :" << *pstr << endl;
    cout << "over " << endl;
}

int main(int argc, char **argv)
{
    test();
    test1();
    return 0;
}
```

![image-20240317153416094](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240317153416094.png)

# Day10

## 第一题

使用嵌套类和静态对象的方式，实现单例模式的自动释放

```cpp
#include <iostream>
using namespace std;

//单例类
class Singleton
{
public:
    static Singleton *getInstance()
    {
        if(nullptr == _pInstance)
        {
            //使用构造函数
            _pInstance = new Singleton();
        }
        return _pInstance;
    }

private:
    class AutoRelese
    {
    public:
        AutoRelese()
        {
            cout << "AutoRelese()" << endl;
        }

        ~AutoRelese()
        {
            cout << "~AutoRelese()" << endl;
            if(_pInstance)
            {
                delete _pInstance;
                _pInstance = nullptr;
            }
        }
    };
private:
    Singleton()
    {
        cout << "Singleton()" << endl;
    }

    ~Singleton()
    {
        cout << " ~Singleton()" << endl;
    }

private:
    static Singleton *_pInstance;
    static AutoRelese _ar;//对象数据成员

};

Singleton *Singleton::_pInstance = nullptr;
Singleton::AutoRelese Singleton::_ar;//创建静态对象


int main(int argc, char * argv[]){
    Singleton *ps1 = Singleton::getInstance();
    return 0;
}
```



## 第二题

在我们建立了基本的写时复制字符串类的框架后，发现了一个遗留的问题。

如果str1和str3共享一片空间存放字符串内容。如果进行读操作，那么直接进行就可以了，不用进行复制，也不用改变引用计数；如果进行写操作，那么应该让str1重新申请一片空间去进行修改，不应该改变str3的内容。

```cpp
cout << str1[0] << endl; //读操作
str1[0] = 'H';         //写操作
cout << str3[0] << endl;//发现str3的内容也被改变了
```

我们首先会想到运算符重载的方式去解决。但是str1[0]返回值是一个char类型变量。

读操作 `cout << char字符 << endl;`

写操作 `char字符 = char字符；`

无论是输出流运算符还是赋值运算符，操作数中没有自定义类型对象，无法重载。而CowString的下标访问运算符的操作数是CowString对象和size_t类型的下标，也没办法判断取出来的内容接下来要进行读操作还是写操作。

—— 提示：创建一个CowString类的内部类，让CowString的operator[]函数返回是这个新类型的对象，然后在这个新类型中对<<和=进行重载，让这两个运算符能够处理新类型对象，从而分开了处理逻辑。



```cpp
 ************************************************************************/
#include <string.h>
#include <iostream>

using std::cout;
using std::endl;

class String
{
public:
    //String s1;
    String()
    : _pstr(new char[5]() + 4)//1 '\0'   2-5 RefCount  int
    {
        cout << "String()" << endl;
        *(int *)(_pstr - 4) = 1;
    }

    //String s1("hello");
    String(const char *pstr)
    : _pstr(new char[strlen(pstr) + 5]() + 4)
    {
        cout << "String(const char *)" << endl;
        strcpy(_pstr, pstr);
        initRefCount();
    }

    //String s2(s1);
    String(const String &rhs)
    : _pstr(rhs._pstr)//浅拷贝
    {
        cout << "String(const String &)" << endl;
        increaseRefCount();
    }


    //String s3("world");
    //s3 = s1
    String &operator=(const String &rhs)
    {
        if(this != &rhs)//1、自复制
        {
            //2、释放左操作
            decreaseRefCount();
            if(0 == *(int *)(_pstr - 4))
            {
                delete [] (_pstr - 4);
            }

            //3、深拷贝(浅拷贝)
            _pstr = rhs._pstr;
            increaseRefCount();
        }
        return *this;//4、返回*this
    }
private:
    class CharProxy
    {
    public:
        CharProxy(String &self,size_t idx)
        : _self(self)
        , _idx(idx)
        {

        }

        char &operator=(const char &ch);//写操作
        
        //类型转换函数
        operator char()
        {
            cout << "operator char() " << endl;
            return  _self._pstr[_idx];
        }
        /* friend std::ostream &operator << (std::ostream &os,const CharProxy &rhs); */

    private:
        String &_self;//此时string是不完整类型
        size_t _idx;
    };

public:
    //下标访问运算符
    //s3[0] = "H"
    //cout <<"s1[0] = " << s1[0] << endl;
    CharProxy operator[](size_t idx)//string *this
    {
        return CharProxy(*this,idx); 
    }

    #if 0
    //下边访问运算符
    //s3[0] = 'H'
    //cout <<"s1[0] = " << s1[0] << endl;
    char &operator[](size_t idx)
    {
        if(idx < size())
        {
            if(getRefCount() > 1)//考虑共享问题
            {
                char *ptmp = new char[size() + 5]() + 4;
                strcpy(ptmp, _pstr);
                decreaseRefCount();

                _pstr = ptmp;
                initRefCount();
            }
            return _pstr[idx];
        }
        else
        {
            static char charnull = '\0';
            return charnull;
        }
    }
    #endif
    

    //s3
    ~String()
    {
        cout << "~String()" << endl;
        release();
    }

public:
    const char *c_str() const
    {
        return _pstr;
    }

    int getRefCount() const
    {
        return *(int *)(_pstr - 4);
    }

private:
    size_t size() const
    {
        return strlen(_pstr);
    }

    void initRefCount()
    {
        *(int *)(_pstr - 4) = 1;
    }

    void increaseRefCount()
    {
        ++*(int *)(_pstr - 4);
    }

    void decreaseRefCount()
    {
        --*(int *)(_pstr - 4);
    }

    void release()
    {
        decreaseRefCount();
        if(0 == getRefCount())
        {
            delete [] (_pstr - 4);
        }
    }


    friend std::ostream &operator<<(std::ostream &os, const String &rhs);
    /* friend std::ostream &operator<<(std::ostream &os, const String::CharProxy &rhs); */
private:
    char *_pstr;
};

std::ostream &operator<<(std::ostream &os, const String &rhs)
{
    if(rhs._pstr)
    {
        os << rhs._pstr;
    }

    return os;
}

//CharProxy里面的函数
//s3[0] = 'H'
char &String::CharProxy::operator=(const char &ch)
{
    if(_idx < _self.size())
    {
        if(_self.getRefCount() > 1)
        {
            char *ptemp = new char[_self.size() + 5]() + 4;
            strcpy(ptemp,_self._pstr);
            _self.decreaseRefCount();
            
            _self._pstr = ptemp;
            _self.initRefCount();
        }
        _self._pstr[_idx] = ch;//真正的赋值操作

        return _self._pstr[_idx];
    }
    else
    {
        static char charnull = '\0';
        return charnull;
    }
}

#if 0
//双友元的设置
std::ostream &operator<<(std::ostream &os,const String::CharProxy &rhs)
{
    os << rhs._self._pstr[rhs._idx];
    return os;
}
#endif

void test()
{
    String s1("hello");
    String s2(s1);
    cout << "s1 = " << s1 << endl;
    cout << "s2 = " << s2 << endl;
    cout << "s1.getRefCount() = " << s1.getRefCount() << endl;
    cout << "s2.getRefCount() = " << s2.getRefCount() << endl;
    printf("s1'address : %p\n", s1.c_str());
    printf("s2'address : %p\n", s2.c_str());

    cout << endl;
    String s3("world");
    cout << "s3 = " << s3 << endl;
    cout << "s3.getRefCount() = " << s3.getRefCount() << endl;
    printf("s3'address : %p\n", s3.c_str());

    cout << endl << "使用s3 = s1进行赋值操作"  << endl;
    s3 = s1;
    cout << "s1 = " << s1 << endl;
    cout << "s2 = " << s2 << endl;
    cout << "s3 = " << s3 << endl;
    cout << "s1.getRefCount() = " << s1.getRefCount() << endl;
    cout << "s2.getRefCount() = " << s2.getRefCount() << endl;
    cout << "s3.getRefCount() = " << s3.getRefCount() << endl;
    printf("s1'address : %p\n", s1.c_str());
    printf("s2'address : %p\n", s2.c_str());
    printf("s3'address : %p\n", s3.c_str());

    cout << endl << "对s3[0]执行写操作" << endl;
    s3[0] = 'H';//'h' = 'H' char = char//CharProxy = char//"hello"
    cout << "s1 = " << s1 << endl;
    cout << "s2 = " << s2 << endl;
    cout << "s3 = " << s3 << endl;
    cout << "s1.getRefCount() = " << s1.getRefCount() << endl;
    cout << "s2.getRefCount() = " << s2.getRefCount() << endl;
    cout << "s3.getRefCount() = " << s3.getRefCount() << endl;
    printf("s1'address : %p\n", s1.c_str());
    printf("s2'address : %p\n", s2.c_str());
    printf("s3'address : %p\n", s3.c_str());

    cout << endl << "对s1[0]执行读操作" << endl;
    cout << "s1[0] = " << s1[0] << endl;//cout << CharProxy//CharProxy -> char;
    cout << "s1 = " << s1 << endl;
    cout << "s2 = " << s2 << endl;
    cout << "s3 = " << s3 << endl;
    cout << "s1.getRefCount() = " << s1.getRefCount() << endl;
    cout << "s2.getRefCount() = " << s2.getRefCount() << endl;
    cout << "s3.getRefCount() = " << s3.getRefCount() << endl;
    printf("s1'address : %p\n", s1.c_str());
    printf("s2'address : %p\n", s2.c_str());
    printf("s3'address : %p\n", s3.c_str());

}
int main(int argc, char **argv)
{
    test();
    return 0;
}
```



# Day11

## 第一题

统计一篇英文(`The_Holy_Bible.txt`)文章中出现的单词和词频。

**输入**：某篇文章的绝对路径

**输出**：词典文件dict.txt（词典中的内容为**每一行**都是一个“**单词 词频**”）

词典的存储格式如下。

```markup
|   a 66          |
|   abandon 77    |
|   public 88     |
|    ...........  |
|_________________|
```

代码设计：

```cpp
struct Record
{
	string _word;
	int _frequency;
};

class Dictionary
{
public:
	//......
    void read(const std::string &filename);
    void store(const std::string &filename);
private:
    vector<Record> _dict;
};
```

**提示**：因为我们需要统计圣经文件中单词以及该单词在文件中出现的次数，所以可以看去读圣经文件，然后将单词存到数据结构中，并记录单词的次数，如果单词第二次出现的时候，只需要修改单词的次数（也就是这里说的单词的频率），这样当统计完整个圣经文件后，数据都存在数据结构vector了。接着遍历vector数据结构就可以将单词以及单词次数(也就是频率)存储到另外一个文件。(当然如果不存到另外一个文件，就只能打印到终端了)

**注意**：在读圣经文件的时候，有可能字符串是不合法的，比如：abc123 abc？这样的字符串，处理方式两种：直接不统计这样的字符串或者将非法字母去掉即可。



```cpp
#include <iostream>
#include<cstring>
#include<vector>
#include<algorithm>
#include<fstream>
#include<sstream>

using std::cout;
using std::endl;
using std::string;
using std::vector;
using std::ifstream;
using std::ofstream;
using std::istringstream;
using std::sort;

struct Record
{
    Record(const string &word,int frequency)
    :_word(word)
    ,_frequency(frequency)
    {

    }
        string _word;
        int _frequency;
};

bool operator<(const Record &lhs, const Record &rhs)
{
    return lhs._word < rhs._word;
}

class Dictionary
{
public:
        //构造函数
    Dictionary (int capacity)
    {
        //设置容器容量
        _dict.reserve(capacity);
    }

    //读取文件，将老的单词处理，新的单词插入到容器里去
    void read(const string &filename);

    //存储文件
    void store(const string &filename);


    //判断字符串里面每个是不是一个字母 abc8989 abc,
    string dealWord(const string &word)
    {
        //判断单词逐个字母
        for(size_t idx = 0;idx != word.size();++idx)
        {
            if(!isalpha(word[idx]))
            {
                return string();
            }
        }

        return word;
    }

    //将新的单词插入到容器中
    void insert(const string &newword)
    {
        //这里是上面dealword函数有返回空字符串的操作的可能
        //这里进行筛选一下
        if(newword == string())
        {
            return;
        }

        //判断容器里的元素，以单词为单位
        size_t idx;
        for(idx = 0;idx != _dict.size(); ++idx)
        {
            //如果这个单词在容器里有重复的单词，
            //词频加一，然后直接退出
            if(newword == _dict[idx]._word)
            {
                ++_dict[idx]._frequency;
                break;
            }
        }

        //走到这里，说明单词第一次出现vector
        if(idx == _dict.size())
        {
            _dict.push_back(Record(newword,1));
        }

    }
private:
    vector<Record> _dict;
};

//Dictionary类里的函数
//
//读取文件，将老的单词处理，新的单词插入到容器里去
void Dictionary::read(const string &filename)
{
    //文件输入流
    ifstream ifs(filename);
    //这里就不写判断状态了，直接取反ifs
    if(!ifs)
    {
        //打印错误信息，并直接返回
        std::cerr << "open" << filename << "fail" << endl;
        return;
    }

    string line;
    while(getline(ifs,line))
    {
        //字符串输入流，把一行line字符串进行操作
        istringstream iss(line);
        string word;
        while(iss >> word)
        {
            //将老的单词进行处理 abc, abc123
            string newword = dealWord(word);
            //将新的单词插入到容器中
            insert(newword);
        }
    }

    //利用sort函数进行对容器里的元素排序
    sort(_dict.begin(), _dict.end());//[,)

    //关闭文件输入流，
    //字符串输入流（istringstream）不需要像文件流那样显式关闭。
    //因为字符串输入流不会打开或占用系统资源，
    //它仅仅是从内存中的字符串中读取数据，所以不需要关闭。
    //一旦使用完毕，它会在离开作用域时自动销毁，释放相关资源。
    ifs.close();
}

//存储文件
void Dictionary::store(const string &filename)
{
    //文件输出流
    ofstream ofs(filename);
    if(!ofs)
    {
        std::cerr << "open" << filename << "fail" << endl;
        return;
    }

    for(size_t idx = 0;idx != _dict.size();++idx)
    {
        ofs << _dict[idx]._word << "  " << _dict[idx]._frequency << endl;
    }

    //同样也要关闭
    ofs.close();
}



int main(int argc, char * argv[])
{
    Dictionary dictionary(13000);

    dictionary.r:swq
        ead("/home/ryc/4.0/file_upload/The_Holy_Bible.txt");
    dictionary.store("dict.data");
    return 0;
}
```



## 第二题

词频统计的作业再用map容器去实现一次，体验一下使用`vector/map`时程序执行的速度

提示：`++dict[word];`



```cpp
#include <iostream>
#include<map>
#include<cstring>
#include<fstream>
#include<sstream>

//测量程序执行时间
#include<chrono>

using namespace std;

class Dictionary
{
public:
    void read(const string &filename)
    {
        ifstream ifs(filename);
        if(!ifs)
        {
            cerr << "ifs open file fail" << endl;
            return;
        }
        string line;
        while(getline(ifs,line))
        {
            istringstream iss(line);
            string word;
            while(iss >> word)
            {
                //处理单词
                string newWord = dealWord(word);

                //将单词的信息插入到map
                if(newWord != string())
                {
                    //一步到位，无过失已经存在的单词，会将frequency+1
                    //如果是新的单词，会插入一个新的pair
                    //string内容为新单词，int 为0，再加为1
                    ++_map[newWord];
                }
            }
        }
        ifs.close();
    }

    string dealWord(string & word)
    {
        for(size_t idx = 0;idx != word.size(); ++idx)
        {
            if(!isalpha(word[idx]))
            {
                //返回空字符串
                return string();
            }
            else if(isupper(word[idx]))
            {
                //大写转小写
                word[idx] = tolower(word[idx]);
            }
        }
        return word;
    }

    void store(const string & filename)
    {
        ofstream ofs(filename);
        if(!ofs)
        {
            cerr << "ofs open file fail" << endl;
            return;
        }

        map<string,int>::iterator it = _map.begin();
        for(;it != _map.end();++it)
        {
            ofs << it->first << " " << it->second << endl;
        }

        ofs.close();
    }
private:
    map<string,int> _map;

};

void test0()
{
    Dictionary dict;
    dict.read("/home/ryc/4.0/file_upload/The_Holy_Bible.txt");
    dict.store("dictMap.dat");
}

int main(int argc, char * argv[]){
    //获取程序开始时间点
    auto start = chrono::high_resolution_clock::now();
    test0();

    //获取程序结束时间点
    auto end = chrono::high_resolution_clock::now();
    //计算时间差
    auto duration = std::chrono::duration_cast<chrono::milliseconds>(end - start);

    //输出执行时间
    cout << "程序执行时间：" << duration.count() << " 毫秒" << endl;
    return 0;
}
```



## 第三题

文本查询
该程序将读取用户指定的任意文本文件【当前目录下的china_daily.txt】，
然后允许用户从该文件中查找单词。查询的结果是该单词出现的次数，并列
出每次出现所在的行。如果某单词在同一行中多次出现，程序将只显示该行
一次。行号按升序显示。

要求：
a. 它必须允许用户指明要处理的文件名字。

b. 程序将存储该文件的内容，以便输出每个单词所在的原始行。
`vector<string> lines;//O(1)`

c. 它必须将每一行分解为各个单词，并记录每个单词所在的所有行。
在输出行号时，应保证以升序输出，并且不重复。

`map<string, set<int> > wordNumbers;`
`map<string, int> dict;`

d. 对特定单词的查询将返回出现该单词的所有行的行号。

e. 输出某单词所在的行文本时，程序必须能根据给定的行号从输入
文件中获取相应的行。

示例：
使用提供的文件内容，然后查找单词 "element"。输出的前几行为：

```
\---------------------------------------------
element occurs 125 times.
(line 62) element with a given key.
(line 64) second element with the same key.
(line 153) element |==| operator.
(line 250) the element type.
(line 398) corresponding element.
\---------------------------------------------
```

程序接口[可选]:

```cpp
class TextQuery
{
public:
//......
void readFile(const string filename);
void query(const string & word);//

private:
//......
vector<string> _lines;//O(1)
map<string, set<int> > _wordNumbers;//the the
map<string, int> _dict;//
};
```

```cpp
//程序测试用例
int main(int argc, char *argv[])
{
string queryWord("hello");

TextQuery tq;
tq.readFile("test.dat");
tq.query(queryWord);
return 0;
}
```

# Day12

## 第一题

三种继承方式对于基类成员的访问权限是怎样的？

| 继承方式                | 基类成员访问权限                                             | 在派生类中访问权限                                           | 派生类对象访问                                               |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 公有继承<br />public    | public<br />protected<br /><span style=color:red;background:yellow>**private**</span> | public<br />protected<br /><span style=color:red;background:yellow>**不可直接访问**</span> | <span style=color:red;background:yellow>**可直接访问**</span><br />不可直接访问<br />不可直接访问 |
| 保护继承<br />protected | public<br />protected<br /><span style=color:red;background:yellow>**private**</span> | public<br />protected<br /><span style=color:red;background:yellow>**不可直接访问**</span> | 不可直接访问                                                 |
| 私有继承<br />private   | public<br />protected<br /><span style=color:red;background:yellow>**private**</span> | private<br />private<br /><span style=color:red;background:yellow>**不可直接访问**</span> | 不可直接访问                                                 |

总之，派生类的访问权限如下总结：

1、不管以什么继承方式，派生类类体中都不能直接访问基类的私有成员<span style=color:red;background:yellow>**（封装性的体现)**</span>

2、不管以什么继承方式，派生类类体中除了基类的私有成员外，其他的都可以访问

3、对于派生类对象而言，只能访问公有继承的基类中的公有成员，其他的都不能访问

**(记忆：1.私有的成员在类外无法直接访问； 2.继承方式和基类成员访问权限做交集)**

## 第二题

多基派生会产生的问题有哪些？怎样解决？

多基派生可能引发的问题：

1、成员名访问冲突(成员函数访问冲突)    

解决方案：类名 + 作用域限定符 -->应当尽量避免

2、 数据成员的存储二义性

解决方案：使用虚拟继承



## 第三题

编写一个圆类Circle，该类拥有：

① 1个成员变量，存放圆的半径； ② 两个构造方法 Circle( ) // 将半径设为0 Circle(double r ) //创建Circle对象时将半径初始化为r ③ 三个成员方法 double getArea( ) //获取圆的面积 double getPerimeter( ) //获取圆的周长 void show( ) //将圆的半径、周长、面积输出到屏幕



```cpp
#include <iostream>

#define PI 3.14

using namespace std;

class Circle
{
public:
    Circle()
    :_r(0)
    {
        cout << "_r = " << _r << endl;
        cout << "Circle()" << endl;
    }
    Circle(double r)
    :_r(r)
    {
        cout << "Circle(double )" << endl;
    }
    
    //获取圆的面积
    double getArea();

    //获取元的周长
    double getPerimeter();

    //将圆的半径、周长、面积输出到屏幕
    void show();
private:
    double _r;
};

//Circle里的成员函数

//获取圆的面积
double Circle::getArea()
{
    return PI * _r * _r;
}

//获取元的周长
double Circle::getPerimeter()
{
    return 2 * PI * _r;
}

//将圆的半径、周长、面积输出到屏幕
void Circle::show()
{
    cout << "圆的半径为： " << _r << endl;
    cout << "圆的面积为： " << getArea() << endl;
    cout << "圆的周长为： " << getPerimeter() << endl;
}

void test0()
{
    Circle cc1(1);
    Circle cc2;
    Circle cc3 = 3;

    cc1.show();
    cc2.show();
    cc3.show();
}

int main(int argc, char * argv[]){
    test0();
    return 0;
}
```



## 第四题

编写一个圆柱体类Cylinder，它继承于上面的Circle类，还拥有：

① 1个成员变量，圆柱体的高；
② 构造方法
Cylinder (double r, double h) //创建Circle对象时将半径初始化为r
③ 成员方法
double getVolume( ) //获取圆柱体的体积
void showVolume( ) //将圆柱体的体积输出到屏幕

 

编写应用程序，创建类的对象，分别设置圆的半径、圆柱体的高，计算并分别显示圆半径、圆面积、圆周长，圆柱体的体积。

```cpp
#include <iostream>

#define PI 3.14

using namespace std;

class Circle
{
public:
    Circle()
    :_r(0)
    {
        cout << "Circle()" << endl;
    }
    Circle(double r)
    :_r(r)
    {
        cout << "Circle(double )" << endl;
    }
    
    //析构函数
    ~Circle()
    {
        cout << "~Circle()" << endl;
    }

    //获取圆的面积
    double getArea();

    //获取元的周长
    double getPerimeter();

    //将圆的半径、周长、面积输出到屏幕
    void show();
private:
    double _r;
};

//圆柱体类Cylinder继承于Circle类
class Cylinder
:public Circle
{
public:
    //构造方法
    Cylinder(double r,double h)
    :Circle(r)
    ,_h(h)
    {
        cout << "Cylinder(double ,double )" << endl;
    }

    //析构函数
    ~Cylinder()
    {
        cout << "~Cylinder()" << endl;
    }

    //成员方法
    //
    //获取圆柱体的体积
    double getVolume();

    //将圆柱体的体积输出到屏幕
    void showVolume();

private:
    double _h;
};


//Circle里的成员函数

//获取圆的面积
double Circle::getArea()
{
    return PI * _r * _r;
}

//获取元的周长
double Circle::getPerimeter()
{
    return 2 * PI * _r;
}

//将圆的半径、周长、面积输出到屏幕
void Circle::show()
{
    cout << "圆的半径为： " << _r << endl;
    cout << "圆的面积为： " << getArea() << endl;
    cout << "圆的周长为： " << getPerimeter() << endl;
}



//Cylinder 类的成员方法
//
//获取圆柱体的体积
double Cylinder::getVolume()
{
    return _h * getArea();
}

//将圆柱体的体积输出到屏幕
void Cylinder::showVolume()
{
    cout << "圆柱体的体积为： " << getVolume() << endl; 
}

//测试函数
void test0()
{
    /* Circle cc1(1); */
    /* Circle cc2; */
    /* Circle cc3 = 3; */

    /* cc1.show(); */
    /* cc2.show(); */
    /* cc3.show(); */
    /* cout << endl << endl; */

    Cylinder cl1(1,1);
    Cylinder cl2(3,3);

    cl1.showVolume();
    cl2.showVolume();
}

int main(int argc, char * argv[]){
    test0();
    return 0;
}
```



## 第五题

构建一个类Person，包含字符串成员name（姓名），整型数据成员age（年龄），成员函数 display()用来输出name和age。构造函数包含两个参数，用来对name和age初始化。构建一个类Employee由Person派生，包含department（部门），实型数据成员salary（工资）,成员函数display（）用来输出职工姓名、年龄、部门、工资，其他成员根据需要自己设定。主函数中定义3个Employee类对象，内容自己设定，将其姓名、年龄、部门、工资输出，并计算他们的平均工资。



```cpp
#include <iostream>
#include<string.h>
#include<iomanip>

using namespace std;

//Person类
class Person
{
public:
    Person(const char *name,const int age)
    :_name(new char[strlen(name) + 1]())
    ,_age(age)
    {
        strcpy(_name,name);
        cout << "Person()" << endl;
    }

    ~Person()
    {
        cout << "~Person" << endl;
    }

    //输出name和age
    void display()
    {
        cout << "name:   " << _name   ;
        cout << "    age: " << _age << endl; 
    }
private:
    char *_name;
    int _age;
};

//Employee类对象
class Employee
:public Person
{
public:
    //构造函数
    Employee(const char *name,const int age,const char *department, double salary)
    :Person(name,age)
    ,_department(new char[strlen(name) + 1]())
    ,_salary(salary)
    {
        cout << "Employee(char *name,int age,char *department,double salary)" << endl;
        strcpy(_department,department);
    }
    void display()
    {
        Person::display();
        cout  << "部门： "<< _department 
            << "  工资： "<< fixed << setprecision(2) << _salary << endl;
    }
    
    //析构函数
    ~Employee()
    {
        cout << "~Employee()" << endl;
    }
private:
    char *_department;//部门
    double _salary;//工资
};


//测试函数
void test0()
{
    Employee ey1("ryc",22,"C++",6.66);
    Employee ey2("syf",23,"java",2.50);
    Employee ey3("gyh",21,"boss",666);

    ey1.Employee::display();
    ey2.Employee::display();
    ey3.Employee::display();
}

int main(int argc, char * argv[]){
    test0();
    return 0;
}

```

# Day13

## 第一题

构建一个类Person，包含字符串成员name（姓名），整型数据成员age（年龄），成员函数 display()用来输出name和age。构造函数包含两个参数，用来对name和age初始化。构建一个类Employee由Person派生，包含department（部门），实型数据成员salary（工资）,成员函数display（）用来输出职工姓名、年龄、部门、工资，其他成员根据需要自己设定。主函数中定义3个Employee类对象，内容自己设定，将其姓名、年龄、部门、工资输出。

要求：用char*来保存字符串内容，并能实现Employee对象的复制、赋值操作。

```cpp
#include <iostream>
#include<string.h>
#include<iomanip>

using namespace std;

//Person类
class Person
{
public:
    Person(const char *name,const int age)
    :_name(new char[strlen(name) + 1]())
    ,_age(age)
    {
        strcpy(_name,name);
        cout << "Person()" << endl;
    }


    //拷贝构造函数
    Person(const Person &rhs)
    :_name(new char [strlen(rhs._name) + 1]())
     ,_age(rhs._age)
    {
        cout << "Person(const Person &rhs)" << endl;
        strcpy(_name,rhs._name);
    }

    //赋值运算符重载
    Person &operator=(const Person &rhs)
    {
        cout << "Person &operator=(const Person &rhs)" << endl;
        if(this != &rhs)
        {
            delete []_name;
            _name = new char[strlen(rhs._name) + 1]();
            strcpy(_name,rhs._name);
            _age = rhs._age;
        }
        return *this;

    }
    
    virtual
    ~Person()
    {
        delete []_name;
        cout << "~Person" << endl;
    }

    //输出name和age
    virtual
    void display() const
    {
        cout << "name:   " << _name   ;
        cout << "    age: " << _age << endl; 
    }
private:
    char *_name;
    int _age;
};

//Employee类对象
class Employee
:public Person
{
public:
    //构造函数
    Employee(const char *name,const int age,const char *department, double salary)
    :Person(name,age)
    ,_department(new char[strlen(department) + 1]())
    ,_salary(salary)
    {
        cout << "Employee(char *name,int age,char *department,double salary)" << endl;
        strcpy(_department,department);
    }

    void display() const override
    {
        Person::display();
        cout  << "部门： "<< _department 
            << "  工资： "<< fixed << setprecision(2) << _salary << endl;
        cout << endl << endl;
    }
    
    Employee(const Employee &rhs) // 复制构造函数
        : Person(rhs)
        , _department(new char[strlen(rhs._department) + 1]())
        , _salary(rhs._salary)
    {
        strcpy(_department, rhs._department);
        cout << "Employee(const Employee&)" << endl;
    }

    Employee &operator=(const Employee &rhs) 
    { // 赋值运算符重载
        if (this != &rhs) 
        {
            Person::operator=(rhs); // 调用基类的赋值运算符
            delete[] _department;
            _department = new char[strlen(rhs._department) + 1]();
            strcpy(_department, rhs._department);
            _salary = rhs._salary;
        }
        cout << "operator=(const Employee&)" << endl;
        return *this;
    }

    //析构函数
    ~Employee() 
    {
        delete [] _department;
        cout << "~Employee()" << endl;
    }
private:
    char *_department;//部门
    double _salary;//工资
};


//测试函数
void test0()
{
    Employee ey1("ryc",22,"C++",6.66);
    Employee ey2("syf",23,"java",2.50);
    Employee ey3("gyh",21,"boss",666);

    ey1 = ey3;
    Employee ey4(ey2);
    /* ey1.Employee::display(); */
    /* ey2.Employee::display(); */
    /* ey3.Employee::display(); */
    /* ey4.Employee::display(); */
    ey1.display();
    ey2.display();
    ey3.display();
    ey4.display();
}

int main(int argc, char * argv[]){
    test0();
    return 0;
}
```



## 第二题

C++中有哪几种多态？请详细说明一下

C++中有：

1、静态多态：包括函数重载，运算符重载，模板。发生的时机在编译的时候。

2、动态多态：虚函数。发生的时机在运行时（./a.out)



## 第三题

在什么情况下析构函数要设置成虚函数？为什么？

一般来说，如果类中定义了虚函数，析构函数也应该被定义为虚析构函数，尤其是类内有申请的动态内存，需要清理和释放的时候。

为什么？

为了解决内存泄漏。





## 第四题

什么是纯虚函数？什么是抽象类？抽象类的作用是什么？

> 纯虚函数（Pure Virtual Function）是C++中的一种特殊类型的成员函数，它在基类中声明但没有提供实现。在C++中，通过在函数声明的末尾添加"= 0"来声明一个纯虚函数。纯虚函数的存在使得所在的类成为抽象类。
>
> 抽象类（Abstract Class）是一个不能被实例化的类，它的存在主要用于作为其他类的基类，定义了一组接口但没有提供具体的实现。抽象类中可以包含普通成员函数、数据成员和纯虚函数。如果一个类至少包含一个纯虚函数，那么它就是一个抽象类。
>
> 抽象类的作用主要有以下几点：
>
> 1. 定义接口：抽象类可以定义一组接口，要求所有从该抽象类派生出的子类都必须实现这些接口，从而确保了派生类的一致性和规范性。
>
> 2. 多态性实现：通过基类指针或引用指向派生类对象，实现多态性。抽象类提供了统一的接口，使得针对基类的操作可以在不同的派生类上实现不同的行为，这是C++面向对象编程中多态性的一种体现。
>
> 3. 防止误用：抽象类的存在可以防止用户错误地实例化基类对象，因为抽象类不能被实例化，只能作为其他类的基类来使用，从而保证了程序的逻辑正确性。



## 第五题

根据给定的程序，写出执行结果

```cpp
#include<iostream>

using std::endl;
using std::cout;

class Base1
{
public:
    virtual void fun()       
    {   
		cout<<"--Base1--\n";  
	}
};

class Base2
{
public:
    void fun()               	
    {   
		cout<<"--Base2--\n"; 
	}
};

class Derived
:public Base1
,public Base2
{
public:
    void fun()
    {   
        cout<<"--Derived--\n";  
    }
};

int main()
{
    Base1 obj1, *ptr1;   
    Base2 obj2, *ptr2;   	
    Derived obj3; 
	

    ptr1=&obj1;         	
    ptr1->fun(); 
    
    ptr2=&obj2;         	
    ptr2->fun(); 
    
    ptr1=&obj3;         	
    ptr1->fun(); 
    
    ptr2=&obj3;         	
    ptr2->fun(); 
    
    return 0;	                  

}
```

```
--Base1--                                                                                  
--Base2--                                                                                  
--Derived--                                                                                
--Base2--    
```



## 第六题

根据给定的程序，写出执行结果

```cpp
class A
{
public:
 void FuncA()
 {
     printf( "FuncA called\n" );
 }
 virtual void FuncB()
 {
     printf( "FuncB called\n" );
 }
};

class B 
: public A
{
public:
 void FuncA()
 {
     A::FuncA();
     printf( "FuncAB called\n" );
 }
    
 virtual void FuncB()
 {
     printf( "FuncBB called\n" );
 }
};

int main( void )
{
	B	b;
	A	*pa;
	pa = &b;
	A *pa2 = new A;
	pa->FuncA(); （ 3） 
	pa->FuncB(); （ 4）
	pa2->FuncA(); （ 5）
	pa2->FuncB();
	delete pa2;
	

	return 0；

}
```

输出为：

```cpp
FuncA called                                                                               
FuncBB called                                                                              
FuncA called                                                                               
FuncB called  
```



## 第七题

根据给定的程序，写出执行结果

```cpp
class Base
{
public:
    Base(int j)
    : i(j) 
    {
        
    }
    virtual  ~Base() 
    {
        
    }
    
    void func1() 
    {
        i *= 10;
        func2();
    }
    
    int getValue()
    {
        return  i;
    }
    
protected:
    virtual void func2()
    {
        i++;
    }
    
protected:
    int i;
};

class Child
: public Base
{
public:
    Child(int j)
    : Base(j) 
    {
        
    }
    void func1()
    {
        i *= 100;
        func2();
    }
    
protected:
    void func2()
    {
        i += 2;
    }
};

int main() 
{
    Base * pb = new Child(1);
    pb->func1();
    cout << pb->getValue() << endl; 
    
	delete pb; 
    
	return 0；
} 
```

输出为：12    

# Day14

## 第一题

关于虚函数的描述中，正确的是（  ）。

- A

  虚函数是一个静态成员函数

- B

  虚函数是一个非成员函数

- C

  虚函数即可以在函数说明定义，也可以在函数实现时定义

- D

  派生类的虚函数与基类中对应的虚函数具有相同的参数个数和类型

## 第二题

关于纯虚函数和抽象类的描述中，错误的是（  ）。

- A

  纯虚函数是一种特殊的虚函数，它没有具体的实现

- B

  抽象类是指具有纯虚函数的类

- C

  一个基类中说明有纯虚函数，该基类派生类一定不再是抽象类

- D

  抽象类只能作为基类来使用，其纯虚函数的实现由派生类给出

  ## 第三题

  在派生类中重新定义虚函数时必须在（）方面与基类保持一致。**(多选题)**

  - A

    参数个数

  - B

    参数类型

  - C

    参数名字

  - D

    操作内容

  - E

    返回类型

    ## 第四题

    一个类有几张虚函数表？请详细说明
    
    0，1，多



## 第五题

带虚函数的单继承结构下，虚函数地址的存放规则是怎样？请用代码验证



## 第六题

带虚函数的多继承结构下，虚函数地址的存放规则是怎样？

# Day15

## 第一题

模板的参数类型有哪些？各自有哪些特点？

模板参数类型（模板参数是可以设置默认值的）

1、类型参数 typename

2、非类型参数，属于整型，short/int/long/long long/bool/void *

(排除double/float两种浮点型)

特点：

类型参数可以写成任意类型

非类型参数在使用时要传入一个整数值

## 第二题

函数模板有几种实例化的方式？函数模板可以重载吗？函数模板的使用需要注意哪些问题？

由函数模板到模板函数的过程称之为实例化

函数模板 --》 生成相应的模板函数 --》编译 ---》链接 --》可执行文件

有<span style=color:red;background:yellow>**两种实例化**</span>：显式实例化和隐式实例化

在进行模板实例化时，并没有指明任何类型，函数模板在生成模板函数时通过传入的参数类型确定出模板类型，这种做法称为隐式实例化。

我们在使用函数模板时还可以在函数名之后直接写上模板的类型参数列表，指定类型，这种用法称为显式实例化。

<span style=color:red;background:yellow>**函数模板可以进行重载**</span>

1. 函数模板与函数模板重载的条件：
   （1）名称相同（这是必须的）
   （2）返回类型不同(并非指推导出的具体返回类型不同，而是返回类型在模板参数列表中
   的位置不同）—— 但是强烈不建议进行这样的重载。
   这样进行重载时，要注意，隐式实例化可能造成冲突，需要显式实例化。（如果能够通过类型转换去匹配上两个函数模板的时候，即使是显式实例化也很难避免冲突）

2. 函数模板与普通函数重载
   普通函数优先于函数模板执行——因为普通函数更快
   （编译器扫描到函数模板的实现时并没有生成函数，只有扫描到下面调用add函数的语句时，给add传参，知道了参数的类型，这才生成一个相应类型的模板函数——模板参数推导。所以使用函数模板一定会增加编译的时间。此处，就直接调用了普通函数，而不去采用函数模板）

## 第三题

利用可变模板参数实现一个函数模板，用来计算多个整型、浮点型数据的加法，返回类型为double



```cpp
#include <iostream>
using namespace std;

//递归出口
double func()
{
    return 0;
}

template <typename T,typename ...Args>//Args模板参数包
double func(T t,Args ...args)
{
    return t + func(args...);
}

void test0()
{
    cout << endl << endl;
    cout << func(2,5.00,6.7) << endl;
}


int main(int argc, char * argv[]){
    test0();
    return 0;
}
```



## 第四题

练习函数模板和类模板的基础用法，多写才能熟悉

# Day16

## 第一题

智能指针的实现原理是什么？用到了什么技术？该技术有哪些特征？

## 第二题

C++提供了哪几种智能指针，其各自的特点是什么？请详细描述

## 第三题

什么是右值引用？C++11为什么要引入右值引用？

## 第四题

std::move函数的作用是什么？

## 第五题

为采用深拷贝方式实现的String类添加移动构造函数和移动赋值运算符函数，并进行测试。

```cpp
String(String &&rhs);
String & operator=(String &&rhs);
```

# Day17 C++基础篇之智能指针

## 第一题

实现C++ primer 15.9中的文本查询扩展的作业，可以查某个单词在某行出现、某个单词在某行没有出现、某两个单词在某行出现、某两个单词至少有一个出现、三个单词的查询等等。(即实现查询单词的与、或、非操作)

# Day18 C++基础篇之标准模板库01

## 第一题

STL包括哪些组件？各自具有哪些特点？

> STL包含六大基本组件：
>
> 1. 容器：用来存数据的，也称为数据结构
>
> 序列式容器：vector、list
>
> 关联式容器：set、map
>
> 无序关联式容器：unordered_set、unordered_map
>
> 2. 迭代器：行为类似于指针，广义指针（具有指针的功能）。用迭代器来连接容器与算法的，可以访问容器中的元素。
> 3. 算法：用来操作数据，操作容器中的元素
> 4. 适配器：起到适配的作用。因为STL中的算法的实现不是针对于具体容器的，所以可能某些算法并不适用具体的容器，需要使用适配器进行适配（进行转接）。
>
> 容器适配器 stack、queue
>
> 迭代器适配器
>
> 函数适配器
>
> 5. 函数对象（仿函数）：定制化操作
> 6. 空间配置器：进行空间的申请与释放操作的。使用+原理+源码
>
> 数据结构+算法 = 程序

## 第二题

序列式容器包括哪些？他们之间有哪些异同？

序列式容器包括：

vector、deuqe、list三种

异同：

初始化：对于三种序列式容器而言，五种初始化的方式都是一样的，无参、count个value、迭代器范围、拷贝或者移动、大括号的。

遍历：三种序列式容器，遍历的方法基本一样，对于vector与deque而言，可以使用下标、迭代器、for
与auto。但是对于list而言，不能使用下标。，但是另外两个遍历方式是可以的

<span style=color:red;background:yellow>**注意：list是没有下标访问的。**</span>

在尾部进行插入与删除：三种序列式容器在尾部进行插入与删除是完全一样的。

在头部进行插入与删除：对于deque与list是可以在头部进行插入与删除，但是vector不行



> 序列式容器包括vector、deque、list三种。它们是C++ STL（标准模板库）中提供的三种常见的序列式容器。
>
> 1. **Vector（向量）**：
>    - 用动态数组实现的序列式容器。
>    - 支持随机访问（通过索引快速访问元素）。
>    - 在尾部插入和删除元素效率高，但在中间或头部插入和删除元素效率较低。
>    - 因为底层使用数组，所以内存空间是连续分配的。
>
> 2. **Deque（双端队列）**：
>    - 双端队列，支持在两端进行快速插入和删除操作。
>    - 支持随机访问，可以像vector一样通过索引访问元素。
>    - 在两端进行插入和删除操作的效率较高，但在中间进行插入和删除操作的效率较低。
>    - 内存空间通常由多个连续的块组成，因此在插入和删除操作时可能需要进行内存重分配和数据复制。
>
> 3. **List（链表）**：
>    - 使用双向链表实现的序列式容器。
>    - 插入和删除操作的效率很高，不受容器大小的影响，无需进行数据移动。
>    - 不支持随机访问，只能通过迭代器顺序访问元素。
>    - 每个元素都是独立分配的，内存空间不一定是连续的。
>
> 异同点：
>
> - **随机访问性能**：vector和deque支持随机访问，可以通过索引快速访问元素，而list不支持随机访问，只能通过迭代器顺序访问元素。
> - **插入和删除效率**：在插入和删除操作方面，list最高效，deque次之，vector效率最低（特别是在中间或头部插入和删除时）。
> - **内存分配方式**：vector和deque的内存分配方式不同，vector的内存空间是连续分配的，而deque的内存空间通常由多个连续的块组成。而list的每个元素都是独立分配的，内存空间不一定是连续的。
> - **空间开销**：由于内存分配方式不同，它们的空间开销也不同。vector通常会分配比实际需要的更多的内存空间，而deque和list则更加灵活，能够根据需要动态分配内存。

## 第三题

创建和初始化vector的方法，每种都给出一个实例？当然也可以把deque与list写出来

<span style=color:red;background:yellow>**vector**</span>

```cpp
#include <iostream>

#include<vector>

using namespace std;

void test0()
{
    //创建一个空的vector
    vector<int> number;
    //添加元素
    number.push_back(10);
    number.push_back(20);
    number.push_back(30);

    //初始化
    vector<int> number_01(10);
    vector<int> number_02(10,4);
    vector<int> number_03 = {1,3,5,7,9,11,13,15};
    int arr[10] = {1,2,3,4,5,6,7,8,9,10};
    vector<int> number_04(arr,arr + 10); 
    
    //遍历
    for(size_t idx = 0;idx != number.size();++idx)
    {
        cout << number[idx] << " ";
    }
    cout << endl;

    //迭代器形式遍历
    vector<int>::iterator it;
    for(it = number_01.begin();it != number_01.end();++it)
    {
        cout << *it << " ";
    }
    cout << endl;

    vector<int>::iterator it2 = number_02.begin();
    for(;it2 != number_02.end();++it2)
    {
        cout << *it2 << " ";
    }
    cout << endl;

    for(auto &elem : number_03)
    {
        cout << elem << " ";
    }
    cout << endl;

    for(auto &elem1 : number_04)
    {
        cout << elem1 << " ";
    }
    cout << endl;

}

int main(int argc, char * argv[])
{
    test0();
    return 0;
}
```

<span style=color:red;background:yellow>**deque**</span>

```cpp
#include <iostream>

#include<deque>

using namespace std;

void test0()
{
    //创建一个空的deque
    deque<int> number;
    //添加元素
    number.push_back(10);
    number.push_back(20);
    number.push_back(30);

    //初始化
    deque<int> number_01(10);
    deque<int> number_02(10,4);
    deque<int> number_03 = {1,3,5,7,9,11,13,15};
    int arr[10] = {1,2,3,4,5,6,7,8,9,10};
    deque<int> number_04(arr,arr + 10); 
   
    //遍历
    for(size_t idx = 0;idx != number.size();++idx)
    {
        cout << number[idx] << " ";
    }
    cout << endl;

    //迭代器形式遍历
    deque<int>::iterator it;
    for(it = number_01.begin();it != number_01.end();++it)
    {
        cout << *it << " ";
    }
    cout << endl;

    deque<int>::iterator it2 = number_02.begin();
    for(;it2 != number_02.end();++it2)
    {
        cout << *it2 << " ";
    }
    cout << endl;

    for(auto &elem : number_03)
    {
        cout << elem << " ";
    }
    cout << endl;

    for(auto &elem1 : number_04)
    {
        cout << elem1 << " ";
    }
    cout << endl;
}

int main(int argc, char * argv[])
{
    test0();
    return 0;
}
```

<span style=color:red;background:yellow>**list**</span>

```cpp
#include <iostream>

#include<list>

using namespace std;

void test0()
{
    //创建一个空的list
    list<int> number;
    //添加元素
    number.push_back(10);
    number.push_back(20);
    number.push_back(30);

    //初始化
    list<int> number_01(10);
    list<int> number_02(10,4);
    list<int> number_03 = {1,3,5,7,9,11,13,15};
    int arr[10] = {1,2,3,4,5,6,7,8,9,10};
    list<int> number_04(arr,arr + 10); 
    
    for(auto &elem : number)
    {
        cout << elem << " ";
    }
    cout << endl;

    //遍历
    /* for(size_t idx = 0;idx != number.size();++idx) */
    /* { */
    /*     cout << number[idx] << " "; */
    /* } */
    /* cout << endl; */

    //迭代器形式遍历
    list<int>::iterator it;
    for(it = number_01.begin();it != number_01.end();++it)
    {
        cout << *it << " ";
    }
    cout << endl;

    list<int>::iterator it2 = number_02.begin();
    for(;it2 != number_02.end();++it2)
    {
        cout << *it2 << " ";
    }
    cout << endl;

    for(auto &elem : number_03)
    {
        cout << elem << " ";
    }
    cout << endl;

    for(auto &elem1 : number_04)
    {
        cout << elem1 << " ";
    }
    cout << endl;

}

int main(int argc, char * argv[])
{
    test0();
    return 0;
}
```

## 第四题

如果c1与c2是两个容器，下面的比较操作有什么限制？

if(c1 < c2)

>
> 在C++中，容器之间的比较操作通常只有在它们的元素类型具有严格弱序关系时才被定义。
>
> 1. **元素类型的限制**：元素类型必须支持 `<` 运算符的比较。这意味着元素类型必须定义了 `<` 运算符，或者你必须提供自定义的比较函数对象。
> 2. **容器类型的限制**：在标准库中，不是所有的容器都支持直接的比较操作。例如，`std::unordered_map` 和 `std::unordered_set` 等无序容器就不支持比较操作。但是，`std::vector`、`std::deque`、`std::list`、`std::set`、`std::map` 等有序容器支持比较操作。
> 3. **容器内元素的要求**：如果容器中的元素是自定义类型，则需要确保该类型定义了 `<` 运算符，或者你必须提供自定义的比较函数对象。
> 4. **容器类型的一致性**：在进行比较操作时，两个容器的类型必须一致。也就是说，你不能比较一个 `std::vector` 和一个 `std::list`。
>
> 综上所述，要使 `if(c1 < c2)` 操作成功，需要满足上述所有的限制条件。如果任何一个条件不满足，编译器会报错。

## 第五题

使用模板实现一个快速排序算法

```cpp
template<typename T,typename Compare=std::less<T>> 
class MyQsort 
{ 
public:     
    MyQsort(T *arr, size_t size, Compare com);     
    void quick(int left, int right, Compare &com);     
    int partition(int left, int right, Compare &com);     
    void print(); 
private:    
    vector<T> _vec; 
};
```

答案:

```cpp
#include <iostream>
#include <vector>
#include <functional> // for std::less

template<typename T, typename Compare = std::less<T>>
class MyQsort {
public:
    MyQsort(T *arr, size_t size, Compare com = Compare()) 
    {
        _vec.assign(arr, arr + size);
        quick(0, size - 1, com);
    }

    void quick(int left, int right, Compare &com) 
    {
        if (left < right) {
            int pivot = partition(left, right, com);
            quick(left, pivot - 1, com);
            quick(pivot + 1, right, com);
        }
    }

    int partition(int left, int right, Compare &com) 
    {
        T pivotValue = _vec[right];
        int i = left - 1;
        for (int j = left; j < right; ++j) 
        {
            if (com(_vec[j], pivotValue)) 
            {
                ++i;
                std::swap(_vec[i], _vec[j]);
            }
        }
        std::swap(_vec[i + 1], _vec[right]);
        return i + 1;
    }

    void print() 
    {
        for (const T &element : _vec) 
        {
            std::cout << element << " ";
        }
        std::cout << std::endl;
    }

private:
    std::vector<T> _vec;
};

int main() 
{
    int arr[] = {4, 65, 2, -31, 0, 99, 83, 782, 1};
    MyQsort<int> sorter(arr, sizeof(arr) / sizeof(arr[0]));
    sorter.print();
    return 0;
}
```



# Day19 C++基础篇之标准模板库02

## 第一题

下面程序有什么错误？

```cpp
list<int> lst; 
list<int>::iterator iter1 = lst.begin(), iter2 = lst.end(); 
while(iter1 < iter2)
{    
     //....
}
```

![image-20240326201506980](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240326201506980.png)

![image-20240326201440374](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240326201440374.png)

```cpp
#include <iostream>

#include<list>
using namespace std;

// 重载 < 运算符
bool operator<(const list<int>::iterator& lhs, const list<int>::iterator& rhs) {
    return &(*lhs) < &(*rhs);
}


int main(int argc, char * argv[])
{
    list<int> lst = {1, 3, 5, 7, 9, 8, 6, 4, 3, 4, 4, 4, 3}; 
    list<int>::iterator iter1 = lst.begin(),iter2 = lst.end(); 
    while(iter1 < iter2)
    {    
        //....
        cout << "iter1 = " << *iter1 << " " << endl;
        ++iter1;
    }
    cout << endl;
    return 0;
}

```



## 第二题

如果c1与c2是两个容器，下面的比较操作有什么限制？

`if(c1 < c2)`

> 在C++中，对于容器的比较操作符 `<`，有一些限制需要注意：
>
> 1. **有序容器**：只有有序容器支持比较操作符 `<`，例如 `std::set`、`std::map`、`std::multiset`、`std::multimap`。
>    
> 2. **相同类型**：要比较的两个容器必须是相同类型，或者是能够进行比较的类型。
>
> 3. **相同元素类型**：两个容器中元素的类型必须支持比较操作符 `<`。如果容器中存储的是用户定义的类型，则该类型必须定义了 `<` 操作符，或者在进行比较时提供自定义的比较函数或者函数对象。
>
> 4. **元素类型的比较定义**：比较操作符 `<` 在容器元素类型上的含义应该是有意义的。例如，在 `std::set` 中，比较操作是基于元素的键值，而在 `std::vector` 中，比较操作符 `<` 则是按照元素在容器中的顺序来进行比较。
>
> 综上所述，如果容器 `c1` 和 `c2` 是相同类型的有序容器，并且容器中存储的元素类型支持比较操作符 `<`，那么 `if(c1 < c2)` 是合法的。

## 第三题

使用模板实现一个堆排序算法

使用模板的框架如下：

```cpp
template <typename T, typename Compare = std::less<T>> 
class HeapSort 
{ 
public:  
    HeapSort(T *arr, size_t size);  
    void heapAdjust(size_t ,size_t);  
    void sort();
    void print();  
private:  
    vector<T> _vec;
    Compare _cmp;
};
```

```cpp
#include <iostream>

#include<algorithm>
#include <vector>

using namespace std;

template <typename T, typename Compare = std::less<T>> 
class HeapSort 
{ 
public:  
    //构造函数
    HeapSort(T *arr, size_t size)
    :_vec(arr,arr + size)
    {
        sort();
    }

    void heapAdjust(size_t parent,size_t length)
    {
        size_t child = parent * 2 + 1;
        T temp = _vec[parent];
        while(child < length)
        {
            if(child + 1 < length && _cmp(_vec[child],_vec[child + 1]))
            {
                ++child;
            }

            if(_cmp(temp,_vec[child]))
            {
                _vec[parent] = _vec[child];
                parent = child;
                child = parent * 2 + 1;
            }
            else
            {
                break;
            }
        }
        _vec[parent] = temp;
    }

    void sort()
    {
        int length = _vec.size();
        for(int i = length / 2 - 1;i >= 0;--i)
        {
            heapAdjust(i,length);
        }

        for(int i = length - 1;i > 0;--i)
        {
            swap(_vec[0],_vec[i]);
            heapAdjust(0,i);
        }
    }

    void print()
    {
        for(const auto &elem : _vec)
        {
            cout << elem << " ";
        }
        cout << endl;
    }

private:  
    vector<T> _vec;
    Compare _cmp;
};

int main(int argc, char * argv[])
{
    int arr[] = {4, 1, 7, 3, 8, 5, 9, 2, 6};
    HeapSort<int> heapSort(arr, sizeof(arr) / sizeof(arr[0]));
    heapSort.print();
    return 0;
}
```

##  第四题

关于vector/deque/list中，push_back和emplace_back的区别是什么？

> `push_back` 和 `emplace_back` 是容器中向尾部添加元素的两种方式，它们的区别主要体现在如何构造新元素以及对性能的影响上。
>
> 1. **push_back**:
>    - `push_back` 接受一个已经构造好的元素，然后将其复制或者移动到容器的尾部。
>    - 当使用 `push_back` 时，需要确保元素类型是可复制或可移动的，因为它需要调用元素类型的拷贝构造函数或移动构造函数来将元素添加到容器中。
>
> ```cpp
> std::vector<int> vec;
> int value = 10;
> vec.push_back(value); // 添加已经构造好的元素到容器尾部
> ```
>
> 2. **emplace_back**:
>    - `emplace_back` 允许在容器尾部直接构造一个新元素，而不需要预先构造好一个元素再添加。
>    - 它接受构造新元素所需的参数，并在容器内部直接进行构造。
>    - 这意味着，`emplace_back` 避免了拷贝或者移动操作，因为它直接在容器内部进行构造。
>
> ```cpp
> std::vector<int> vec;
> vec.emplace_back(10); // 在容器尾部直接构造新元素
> ```
>
> 总的来说，`emplace_back` 在性能上可能比 `push_back` 更高效，因为它避免了额外的拷贝或移动操作，特别是当元素类型不是简单的内置类型时，使用 `emplace_back` 可以更好地利用移动语义和完美转发。

# Day20 C++基础篇之标准模板库02

## 第一题

关联式容器有哪些？各自具有哪些特点？（熟悉基本操作）

目前学习的关联式容器有`std::set`和`std::map`,关联式容器是一种数据结构，他们存储的数据以键-值对的形式组织，并允许通过键来快速查找和访问值。

这些容器的特点如下：

set的特征：

1. 存放的是key类型，key值是唯一的，不能重复
2. 默认会按照key值进行升序排列（从小到大）
3. 底层使用的是红黑树

multiset特征：

1. 可以存放关键字key相同的元素，关键字不唯一
2. 默认以升序进行排列
3. 底层实现是红黑树

map的特征：

1. 存放的是键值对，也就是多个pair，即pair<const key,value>,key值必须唯一，不能重复
2. 默认按照关键字key进行升序排列
3. 底层实现是红黑树

multimap的特征：

1. 存放的是键值对，也就是多个pair，即pair<const key,value>，key值不唯一，可以重复
2. 默认按照关键字key进行升序排列
3. 底层实现是红黑树



## 第二题

使用map重写词频统计作业。（之前使用的vector，可以比较他们的速率）



```cpp
#include <iostream>
#include<map>
#include<cstring>
#include<fstream>
#include<sstream>

//测量程序执行时间
#include<chrono>

using namespace std;

class Dictionary
{
public:
    void read(const string &filename)
    {
        ifstream ifs(filename);
        if(!ifs)
        {
            cerr << "ifs open file fail" << endl;
            return;
        }
        string line;
        while(getline(ifs,line))
        {
            istringstream iss(line);
            string word;
            while(iss >> word)
            {
                //处理单词
                string newWord = dealWord(word);

                //将单词的信息插入到map
                if(newWord != string())
                {
                    //一步到位，无过失已经存在的单词，会将frequency+1
                    //如果是新的单词，会插入一个新的pair
                    //string内容为新单词，int 为0，再加为1
                    ++_map[newWord];
                }
            }
        }
        ifs.close();
    }

    string dealWord(string & word)
    {
        for(size_t idx = 0;idx != word.size(); ++idx)
        {
            if(!isalpha(word[idx]))
            {
                //返回空字符串
                return string();
            }
            else if(isupper(word[idx]))
            {
                //大写转小写
                word[idx] = tolower(word[idx]);
            }
        }
        return word;
    }

    void store(const string & filename)
    {
        ofstream ofs(filename);
        if(!ofs)
        {
            cerr << "ofs open file fail" << endl;
            return;
        }

        map<string,int>::iterator it = _map.begin();
        for(;it != _map.end();++it)
        {
            ofs << it->first << " " << it->second << endl;
        }

        ofs.close();
    }
private:
    map<string,int> _map;

};

void test0()
{
    Dictionary dict;
    dict.read("/home/ryc/4.0/file_upload/The_Holy_Bible.txt");
    dict.store("dictMap.dat");
}

int main(int argc, char * argv[]){
    //获取程序开始时间点
    auto start = chrono::high_resolution_clock::now();
    test0();

    //获取程序结束时间点
    auto end = chrono::high_resolution_clock::now();
    //计算时间差
    auto duration = std::chrono::duration_cast<chrono::milliseconds>(end - start);

    //输出执行时间
    cout << "程序执行时间：" << duration.count() << " 毫秒" << endl;
    return 0;
}
```

# Day21 C++基础篇之标准模板库02

## 第一题

无序关联式容器有哪些？各自具有哪些特点？

无序关联式容器有：

unordered_set、unordered_multiset、unordered_map、unorder_multimap

特征：

unordered_set：

1. 存放的是key类型，key值是唯一的，不能重复
2. 元素是没有顺序的
3. 底层使用的是哈希

unordered_multiset：

1. 存放的是key类型，key值<span style=color:red;background:yellow>**不是**</span>唯一的，<span style=color:red;background:yellow>**可以重复**</span>
2. 元素是没有顺序的
3. 底层使用的是哈希

unordered_map：

1. 存放的是key-value类型，key值是唯一的，不能重复；但是value值是可以重复的
2. key值是没有顺序的
3. 底层实现是用的是哈希

unorder_multimap：

1. 存放的是key-value类型，key值<span style=color:red;background:yellow>**不是唯一**</span>的，<span style=color:red;background:yellow>**可以重复**</span>；但是value值是可以重复的
2. key值是没有顺序的
3. 底层实现是用的是哈希

## 第二题

使用unordered_map重写词频统计作业。再比较一下使用map和vector时所花费的时间，体会这几种容器的区别



```cpp
#include <iostream>

#include<unordered_map>
#include<cstring>
#include<fstream>
#include<sstream>

//测量程序执行时间
#include<chrono>

using namespace std;

class Dictionary
{
public:
    void read(const string &filename)
    {
        ifstream ifs(filename);
        if(!ifs)
        {
            cerr << "ifs open file fail" << endl;
            return;
        }
        string line;
        while(getline(ifs,line))
        {
            istringstream iss(line);
            string word;
            while(iss >> word)
            {
                //处理单词
                string newWord = dealWord(word);

                //将单词的信息插入到unordered_map
                if(newWord != string())
                {
                    //一步到位，无过失已经存在的单词，会将frequency+1
                    //如果是新的单词，会插入一个新的pair
                    //string内容为新单词，int 为0，再加为1
                    ++_unordered_map[newWord];
                }
            }
        }
        ifs.close();
    }

    string dealWord(string & word)
    {
        for(size_t idx = 0;idx != word.size(); ++idx)
        {
            if(!isalpha(word[idx]))
            {
                //返回空字符串
                return string();
            }
            else if(isupper(word[idx]))
            {
                //大写转小写
                word[idx] = tolower(word[idx]);
            }
        }
        return word;
    }

    void store(const string & filename)
    {
        ofstream ofs(filename);
        if(!ofs)
        {
            cerr << "ofs open file fail" << endl;
            return;
        }

        auto it = _unordered_map.begin();
        for(;it != _unordered_map.end();++it)
        {
            ofs << it->first << " " << it->second << endl;
        }

        ofs.close();
    }
private:
    unordered_map<string,int> _unordered_map;

};

void test0()
{
    Dictionary dict;
    dict.read("/home/ryc/4.0/file_upload/The_Holy_Bible.txt");
    dict.store("dictunordered_map.dat");
}

int main(int argc, char * argv[]){
    //获取程序开始时间点
    auto start = chrono::high_resolution_clock::now();
    test0();

    //获取程序结束时间点
    auto end = chrono::high_resolution_clock::now();
    //计算时间差
    auto duration = std::chrono::duration_cast<chrono::milliseconds>(end - start);

    //输出执行时间
    cout << "程序执行时间：" << duration.count() << " 毫秒" << endl;
    return 0;
}
```

![image-20240328201150144](http://ryctypora01.oss-cn-hangzhou.aliyuncs.com/img/image-20240328201150144.png)

## 第三题

使用unordered_map/map实现单词转换程序。给定一个string，将它转换为另一个string。程序的输入是两个文件，第一个文件保存的是一些规则，用来转换第二个文件中的文本。每条规则由两部分组成：一个可能出现在输入文件中的单词和一个用来替换它的短语。表达的含义是，每当第一个单词出现在输入中时，我们就将它替换为对应的短语，第二个输入文件包含要转换的文本。（C++ primer 11.3.6）

提示：

规则文件：map.txt文件，其实就是第一个单词，被后面的一串所替换。

待转换文本：file.txt文件，该文件中的单词如果出现在map.txt文件的第一个单词的话，就用map.txt的后面一串替代。

结果：最后结果其实就是，将file.txt文件中的并且出现在map.txt中第一个单词转换为map.txt后面的一串。例如：where r u的输出结果就是where are you. r替换为are，u替换为you


//file.txt
where r u
y dont u send me a pic
k thk l8r

//map.txt
brb be right back
k okay?
y why
r are
u you
pic picture
thk thanks!
l8r later



```cpp
#include <iostream>
#include <fstream>
#include <map>
#include <string>
#include <sstream>
#include <algorithm>
using namespace std;




map<string,string> buildMap(ifstream &map_file)
{
    //保存转换规则
    map<string,string> trans_map;
    //要转换的单词
    string key;
    //替换后的内容
    string value;
    //读取第一个单词存入key中，行中剩余内容存入value
    while(map_file >> key && getline(map_file,value))
    {
        //检查是否有转换规则
        if(value.size() > 1)
        {
            trans_map[key] = value.substr(1);//跳过前导空格
        }
        else
        {
            throw runtime_error("no rule for " + key);
        }
    }
    return trans_map;
}

const string &transform(const string &s,const map<string,string> &m)
{
    //实际的转换工作；此部分是程序的核心
    auto map_it = m.find(s);
    //如果单词在转换规则map中
    if(map_it != m.cend())
    {
        return map_it->second;  //使用替换短语
    }
    else
    {
        return s;   //否则返回原string
    }
}

void word_transform(ifstream &map_file,ifstream &input)
{
    //保存转换规则
    auto trans_map = buildMap(map_file);
    //保存输入的每一行
    string text_line;
    //读取一行输入
    while(getline(input,text_line))
    {
        //读取每个单词
        istringstream stream(text_line);
        string word;
        //控制是否打印空格
        bool firstWord = true;
        while(stream >> word)
        {
            if(firstWord)
            {
                firstWord = false;
            }
            else
            {
                cout << " ";//单词间打印一个空格
            }
            //transform返回它的第一个参数或其转换后的形式
            cout << transform(word,trans_map);//打印输出
        }
        cout << endl;   //完成一行的转换
    }
}


int main(int argc, char * argv[]){
    if (argc != 3)
        throw runtime_error("wrong number of arguments");
    ifstream map_file(argv[1]);    //第一个参数为file.txt文件
    if (!map_file)
        throw runtime_error("no transformation file");
    ifstream input(argv[2]);      //第二个参数为map.txt文件
    if (!input)
        throw runtime_error("no input file");
    word_transform(map_file, input);
    return 0;
}

```



## 第四题

Leetcode 146 LURCache的实现

https://leetcode-cn.com/problems/lru-cache/

请你设计并实现一个满足 LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

示例：

输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1); // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2); // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1); // 返回 -1 (未找到)
lRUCache.get(3); // 返回 3
lRUCache.get(4); // 返回 4


提示：

1 <= capacity <= 3000
0 <= key <= 10000
0 <= value <= 105
最多调用 2 * 105 次 get 和 put

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/lru-cache
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



```cpp
/*************************************************************************
# File Name: 4_LRU.cc
# Author: RoyceJen
# email: roycejen@163.com
# Created Time: Mon 01 Apr 2024 07:58:34 PM CST
 ************************************************************************/
#include <iostream>
#include <list>
#include <unordered_map>
#include <utility>

using namespace std;

class LRU
{
public:
    LRU(int cap)
    : _capacity(cap)
    {

    }

    void put(int key,int value)
    {
        auto it = _cache.find(key);
        //该key在链表中了
        if(it != _cache.end())
        {
            //更新value
            it->second->second = value;
            //移动key与value再_nodes(list) 中的位置
            _nodes.splice(_nodes.begin(),_nodes,it->second);
        }
        else
        {
            //该key值不在链表中
            //如果是list是满的，
            if(_capacity == (int) _nodes.size())
            {
                //获取list的最后一个元素
                auto &deleteNode = _nodes.back();
                //删除掉最后一个元素
                _nodes.pop_back();
                _cache.erase(_cache.find(deleteNode.first));
            }

            //将key值与value插入到list的最前面
            _nodes.push_front(pair<int,int>(key,value));
            //同时更新到unordered_map中
            _cache[key] = _nodes.begin();
        }
    }

    int get(int key)
    {
        auto it = _cache.find(key);
        if(it != _cache.end())
        {
            _nodes.splice(_nodes.begin(),_nodes,it->second);
            return it->second->second;
        }
        else
        {
            return -1;
        }
    }

    void print() const
    {
        for(auto &elem : _nodes)
        {
            cout << elem.first << " " << elem.second << " ";
        }
        cout << endl;
    }
private:
    int _capacity;//元素的个数
    list<pair<int,int>> _nodes;//存放元素的节点
    unordered_map<int,list<pair<int,int>>::iterator> _cache;
};


void test()
{
    LRU lru(4);
    lru.put(1, 10);
    lru.print();

    cout << endl;
    lru.put(2, 20);
    lru.print();

    cout << endl;
    cout << "lru.get(1) = " << lru.get(1) << endl;
    lru.print();

    cout << endl;
    cout << "lru.get(2) = " << lru.get(2) << endl;
    lru.print();

    cout << endl;
    lru.put(3, 30);
    lru.print();

    cout << endl;
    cout << "lru.get(2) = " << lru.get(2) << endl;
    lru.print();

    cout << endl;
    lru.put(3, 33);
    lru.print();

    cout << endl;
    lru.put(4, 40);
    lru.print();

    cout << endl;
    lru.put(5, 50);
    lru.print();

    cout << endl;
    cout << "lru.get(1) = " << lru.get(1) << endl;
    lru.print();

}

int main(int argc, char * argv[])
{
    test();
    return 0;
}

```



# Day22 C++基础篇之标准模板库05

## 第一题

容器、迭代器、算法之间的关系是怎样的？他们是如何结合在一起的？

> 容器、迭代器和算法是C++标准库中的三个核心概念，它们之间密切相关并且相互配合。
>
> 1. **容器（Containers）**：容器是一种数据结构，用于存储一组对象。C++标准库提供了各种类型的容器，如vector、list、map等。这些容器提供了不同的接口和性能特征，以满足不同的需求。容器可以包含各种类型的数据，例如基本数据类型、自定义类型或者其他容器。
>
> 2. **迭代器（Iterators）**：迭代器是一种提供对容器元素访问的抽象概念。它允许以一种统一的方式遍历容器中的元素，而不需要了解底层容器的具体实现细节。迭代器可以被视为一种类似指针的对象，它指向容器中的特定元素，并且可以通过递增或递减操作来遍历容器中的元素。迭代器通常用于循环结构中，以便遍历容器并对其中的元素执行操作。
>
> 3. **算法（Algorithms）**：算法是对数据执行操作的过程或方法。C++标准库提供了大量的算法，例如排序、搜索、转换等。这些算法可以应用于各种容器，通过迭代器来实现对容器中元素的操作。算法可以完成各种任务，例如对容器进行排序、查找特定元素、应用函数到容器中的每个元素等。
>
> 这三个概念之间的关系在于它们相互配合，使得在C++中可以方便地对数据进行处理和操作。通常情况下，算法通过迭代器来访问容器中的元素，从而实现对容器的操作。因此，使用算法时需要将迭代器作为参数传递给算法，以指定操作的对象。而容器则提供了存储数据的结构，使得算法和迭代器能够操作其中的元素。

## 第二题

什么是迭代器失效问题？该问题是如何产生的？怎样避免产生迭代器失效问题

> 迭代器失效问题是指在对容器进行操作时，使得指向容器元素的迭代器无效的情况。这可能会导致未定义行为，甚至程序崩溃。迭代器失效通常由于对容器进行修改操作而引起，例如插入、删除或者重新分配内存等。
>
> 迭代器失效问题可以由以下几种情况引起：
>
> 1. **插入和删除操作**：当在容器中插入或删除元素时，可能会导致指向容器元素的迭代器失效。这是因为插入或删除操作可能导致容器重新分配内存或者重新组织元素的存储方式，使得之前指向的元素位置已经不再有效。
>
> 2. **重新分配内存**：某些容器在元素数量增长时可能会重新分配内存以扩展容量，例如vector。在这种情况下，之前指向容器元素的迭代器可能会失效，因为重新分配会导致原有的元素在内存中位置发生改变。
>
> 3. **容器的复制和赋值**：在对容器进行复制或者赋值操作时，例如使用赋值操作符或者复制构造函数，可能会导致之前的迭代器在新的容器中失效。
>
> 为了避免迭代器失效问题，可以考虑以下几种方法：
>
> 1. **使用正确的迭代器**：在对容器进行操作时，确保使用正确类型的迭代器，并且及时更新迭代器以避免失效。
>
> 2. **避免插入和删除操作**：尽量避免在迭代器遍历容器的同时对容器进行插入和删除操作。如果必须进行插入和删除操作，考虑使用返回新的迭代器的插入和删除函数，以确保操作完成后仍然保留有效的迭代器。
>
> 3. **使用智能指针或者迭代器适配器**：某些情况下，可以使用智能指针或者迭代器适配器来代替原始迭代器。智能指针具有更复杂的语义，并且在容器改变时可以自动调整。迭代器适配器可以提供额外的保护措施，帮助避免迭代器失效问题。
>
> 4. **在必要时重新获取迭代器**：如果在对容器进行操作后迭代器可能失效，可以考虑在每次需要使用迭代器时重新获取迭代器，以确保其有效性。

## 第三题

Leetcode 20题

https://leetcode-cn.com/problems/valid-parentheses/

 

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
每个右括号都有一个对应的相同类型的左括号。


示例 1：

输入：s = "()"
输出：true
示例 2：

输入：s = "()[]{}"
输出：true
示例 3：

输入：s = "(]"
输出：false


提示：

1 <= s.length <= 104
s 仅由括号 '()[]{}' 组成

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/valid-parentheses
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



```cpp
class Solution {
public:
    bool isValid(string s) 
    {
        unordered_map<char,int> brackets
        {
            {'(',1},
            {'[',2},
            {'{',3},
            {')',4},
            {']',5},
            {'}',6}
        };
        stack<char> st;
        bool isTrue = true;
        for(char c : s)
        {
            int flag = brackets[c];
            if(flag >= 1&&flag <= 3)
            {
                st.push(c);
            }
            else if(!st.empty() && brackets[st.top()] == flag-3)
            {
                st.pop();
            }
            else
            {
                isTrue = false;
                break;
            } 
        }

        if(!st.empty())
        {
            isTrue = false;
        }
        return isTrue;
    }
};
```



## 第四题

Leetcode 127题

https://leetcode-cn.com/problems/word-ladder/

 

字典 wordList 中从单词 beginWord 和 endWord 的 转换序列 是一个按下述规格形成的序列 beginWord -> s1 -> s2 -> ... -> sk：

每一对相邻的单词只差一个字母。
 对于 1 <= i <= k 时，每个 si 都在 wordList 中。注意， beginWord 不需要在 wordList 中。
sk == endWord
给你两个单词 beginWord 和 endWord 和一个字典 wordList ，返回 从 beginWord 到 endWord 的 最短转换序列 中的 单词数目 。如果不存在这样的转换序列，返回 0 。


示例 1：

输入：beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
输出：5
解释：一个最短转换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog", 返回它的长度 5。
示例 2：

输入：beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
输出：0
解释：endWord "cog" 不在字典中，所以无法进行转换。


提示：

1 <= beginWord.length <= 10
endWord.length == beginWord.length
1 <= wordList.length <= 5000
wordList[i].length == beginWord.length
beginWord、endWord 和 wordList[i] 由小写英文字母组成
beginWord != endWord
wordList 中的所有字符串 互不相同

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/word-ladder
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

# Day23 C++基础篇之标准模板库06

## 第一题

算法库中有哪些类型的操作？什么是函数对象？

> 算法分类：
>
> 非修改式的算法 **for_each**、count、find、search
>
> 修改式的算法 **copy**、**remove_if**、replace、swap
>
> 排序算法 **sort**
>
> 二分搜索 lower_bound、upper_bound
>
> 集合操作 **set_intersection**、set_union、set_difference
>
> 堆操作 make_heap、push_heap、pop_heap
>
> 取最值 max、min
>
> 数值操作 atoi
>
> 未初始化的内存操作 **uninitialized_copy**
>
> 什么是函数对象：
>
> 所有与小括号进行结合展示函数含义的对象。
>
> - 函数名
> - 函数指针
> - 重载了函数调用运算符的类创建的对象

## 第二题

容器、迭代器、算法之间的关系是怎样的？他们是如何结合在一起的？

> 容器、迭代器和算法是C++标准库中的三个核心概念，它们之间密切相关并且相互配合。
>
> 1. **容器（Containers）**：容器是一种数据结构，用于存储一组对象。C++标准库提供了各种类型的容器，如vector、list、map等。这些容器提供了不同的接口和性能特征，以满足不同的需求。容器可以包含各种类型的数据，例如基本数据类型、自定义类型或者其他容器。
>
> 2. **迭代器（Iterators）**：迭代器是一种提供对容器元素访问的抽象概念。它允许以一种统一的方式遍历容器中的元素，而不需要了解底层容器的具体实现细节。迭代器可以被视为一种类似指针的对象，它指向容器中的特定元素，并且可以通过递增或递减操作来遍历容器中的元素。迭代器通常用于循环结构中，以便遍历容器并对其中的元素执行操作。
>
> 3. **算法（Algorithms）**：算法是对数据执行操作的过程或方法。C++标准库提供了大量的算法，例如排序、搜索、转换等。这些算法可以应用于各种容器，通过迭代器来实现对容器中元素的操作。算法可以完成各种任务，例如对容器进行排序、查找特定元素、应用函数到容器中的每个元素等。
>
> 这三个概念之间的关系在于它们相互配合，使得在C++中可以方便地对数据进行处理和操作。通常情况下，算法通过迭代器来访问容器中的元素，从而实现对容器的操作。因此，使用算法时需要将迭代器作为参数传递给算法，以指定操作的对象。而容器则提供了存储数据的结构，使得算法和迭代器能够操作其中的元素。

## 第三题

什么是回调函数，注册回调函数，执行回调函数？(掌握std::bind用法,非常重要)

std::bind的实现原理阅读材料

```
http://www.cnblogs.com/xusd-null/p/3698969.html
https://www.tuicool.com/articles/iMZ7ba
```

> 
> 回调函数是一种在编程中常见的机制，用于在某个事件发生时执行用户定义的代码。它通常用于异步编程、事件驱动编程和回调式接口中。
>
> 1. **回调函数（Callback Function）**：回调函数是指在某个事件发生时由系统或者框架调用的函数。这个函数由用户自己编写，用于处理特定的事件或者情况。当事件发生时，系统会调用注册的回调函数来执行相应的操作。
> 2. **注册回调函数（Register Callback Function）**：注册回调函数是指将用户定义的回调函数与特定的事件或者条件关联起来。这通常涉及到在系统或框架中注册回调函数的地址或者函数指针，以便在事件发生时能够正确地调用相应的回调函数。
> 3. **执行回调函数（Invoke Callback Function）**：执行回调函数是指在事件发生时，系统或框架调用注册的回调函数来执行相应的操作。这通常会传递一些参数给回调函数，以便回调函数能够获取事件发生时的上下文信息，并执行相应的逻辑。
>
> 在C++中，可以使用`std::function`和`std::bind`来实现回调函数的注册和执行。
>
> - **std::function**：`std::function`是一个通用的函数封装器，可以包装任何可调用对象（函数、函数指针、成员函数指针、Lambda表达式等），并且可以通过`operator()`来调用包装的函数对象。
>
> - **std::bind**：`std::bind`用于创建一个函数对象，将函数和其参数绑定在一起。可以使用`std::bind`来绑定一个函数和其参数，并将绑定后的函数对象作为回调函数注册到系统或框架中。在绑定时，可以使用占位符（`std::placeholders::_1`、`std::placeholders::_2`等）来表示待绑定的参数位置。
>
>   下面是一个示例，演示了如何使用`std::bind`来注册和执行回调函数：
>
>   ```
>   cppCopy code#include <iostream>
>   #include <functional>
>               
>   // 定义回调函数
>   void callback(int a, int b) {
>       std::cout << "Callback function called with arguments: " << a << " and " << b << std::endl;
>   }
>               
>   int main() {
>       // 使用 std::bind 绑定回调函数和参数
>       auto callback_func = std::bind(callback, std::placeholders::_1, std::placeholders::_2);
>               
>       // 执行回调函数
>       callback_func(10, 20);
>               
>       return 0;
>   }
>   ```
>
>   在这个示例中，我们定义了一个回调函数`callback`，然后使用`std::bind`将其与参数绑定在一起，创建了一个函数对象`callback_func`。最后，我们通过调用`callback_func`来执行回调函数，并传递参数10和20给回调函数。

## 第四题

了解std::allocator的用法之后,实现自定义的Vector类

接口形式：

```cpp
template<typename T> 
class Vector 
{ 
public:     
    Vector();     
    ~Vector();          
    void push_back(const T &);      
    void pop_back();         
    int size();     
    int capacity(); 
private:     
    void reallocate();//重新分配内存,动态扩容要用的 
private:         
    static std::allocator<T> _alloc;          
    T *_start;                 //指向数组中的第一个元素     
    T *_finish;                //指向最后一个实际元素之后的那个元素     
    T *_end_of_storage;        //指向数组本身之后的位置 
};
 Vector模型
 ______________________________
 |_|_|_|_|_|____________________|
 ↑          ↑                    ↑
 _start   _finish            _end_of_storage
```



```cpp
#include <iostream>
#include <memory>

using namespace std;

template<typename T>
class Vector
{
public:
    Vector()
    :_start(nullptr)
    ,_finish(nullptr)
    ,_end_of_storage(nullptr)
    {

    }

    ~Vector();

    typedef T* iterator;
    iterator begin()
    {
        return _start;
    }

    iterator end()
    {
        return _finish;
    }

    int size() const
    {
        return _finish - _start;
    }

    int capacity() const
    {
        return _end_of_storage - _start;
    }

    void push_back(const T &);
    void pop_back();

private:
    void reallocate();  //重新分配内存，动态扩容要用的
private:
    static allocator<T> _alloc;

    T *_start;  //指向数组中的第一个元素
    T * _finish; //只想最后一个实际元素之后的那个元素
    T * _end_of_storage;    //指向数组本身之后的位置
};

template <typename T>
allocator<T> Vector<T>::_alloc;

template <typename T>
Vector<T> :: ~Vector()
{
    if(_start)
    {
        while(_finish != _start)
        {
            _alloc.destroy(--_finish);//对象的销毁
        }
        _alloc.deallocate(_start,capacity());//空间的释放
    }

}

template<typename T>
void Vector<T>::push_back(const T &value)
{
    if(size() == capacity())
    {
        reallocate();//调用扩容函数
    }

    if(size() < capacity())
    {
        //在指定的位置构建对象
        _alloc.construct(_finish++,value);
    }
}

template<typename T>
void Vector<T>::pop_back()
{
    if(size() > 0)
    {
        //销毁对象
        _alloc.destroy(--_finish);
    }
}


template<typename T>
void Vector<T>::reallocate()
{
    //1. 申请一块新的空间
    int oldCapacity = capacity();
    int newCapacity = oldCapacity > 0 ? 2 * oldCapacity : 1;
    T *ptmp = _alloc.allocate(newCapacity);
    if(_start)
    {
        //2. 将老的空间上的元素拷贝到新空间
        uninitialized_copy(_start,_finish,ptmp);
        //3. 将老的空间上的对象销毁并且将空间进行回收
        while(_finish != _start)
        {
            _alloc.destroy(--_finish);
        }
        _alloc.deallocate(_start,oldCapacity);
    }
    //4. 将3个指针与新空间产生关系
    _start = ptmp;
    _finish = _start + oldCapacity;
    _end_of_storage = _start + newCapacity;
}

template <class Container>
void display(const Container &vec)
{
    cout << "vec.size() = " << vec.size() << endl
        <<"vec.capacity() = " << vec.capacity() << endl;
}

void test()
{
    Vector<int> numbers;
    display(numbers);

    cout << endl;
    numbers.push_back(1);
    display(numbers);

    cout << endl;
    numbers.push_back(2);
    display(numbers);

    cout << endl;
    numbers.push_back(3);
    display(numbers);

    cout << endl;
    numbers.push_back(4);
    display(numbers);

    cout << endl;
    numbers.push_back(5);
    display(numbers);

    cout << endl;
    for(auto &elem : numbers)
    {
        cout << elem << " ";
    }
    cout<< endl;;
}

int main(int argc, char * argv[])
{
    test();
    return 0;
}
```



## 第五题

阅读源码：结合STL中std::allocator的源码阅读《STL源码剖析》的第二章内容

# Day24

wu
