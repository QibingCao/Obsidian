第一题

**C++内存布局是怎样的？可以具体阐述一下么？**

> - 栈区：操作系统控制，由高地址向低地址生长。
>
> - 堆区：程序员分配，由低地址向高地址生长，堆区与栈区没有明确的界限。
>
> - 全局/静态区：读写段（数据段），存放全局变量、静态变量。
>
> - 文字常量区：只读段，存放程序中直接使用的常量，如const char * p = "hello";  hello这个内容就存在文字常量区。
>
> - 程序代码区：只读段，存放函数体的二进制代码。







第二题

**什么是拷贝构造函数，其形态是什么，参数可以修改吗？**

> 拷贝构造是一个编译自动提供的构造函数，用于对象的复制操作。当显式定义了拷贝构造函数后，编译器不再提供默认的拷贝构造函数。
>
> 
>
> 形态为 
>
> ``` c++
> 类名(const 类名 & 形参){
>     //...
> }
> ```
>
> 
>
> 拷贝构造函数的参数不能修改。
>
> 如果不加引用，在进行复制操作时调用拷贝构造，但在实参初始化形参时会再次触发拷贝构造调用，使得拷贝构造陷入循环调用，直到栈溢出，程序出错。
>
> 如果不加const，在拷贝构造中可以修改其参数的内容，这并不应该被允许。同时，取消参数中的const也使得通过临时对象复制出新对象的操作不可通过，
>
> 因为非const引用不能绑定右值。
>
> 







第三题

**什么情况下，会调用拷贝构造函数?**

> 1. 当使用一个已经存在的对象初始化另一个同类型的新对象时；
>
> 2. 当函数参数（实参和形参的类型都是对象），形参与实参结合时（实参初始化形参）；
>
>    —— 为了避免这次不必要的拷贝，可以使用引用作为参数
>
> 3. 当函数的返回值是对象，执行return语句时（编译器有优化,使用去优化参数-fno-elide-constructors可以看到效果）。
>
>    ——为了避免这次多余的拷贝，可以使用引用作为返回值，但一定要确保返回值的生命周期大于函数的生命周期







第四题

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

> 手动delete pa,即手动回收a对象，最先调用A类析构函数；
>
> b对象创建在栈区，函数栈生命周期结束时，销毁b对象，调用B类析构函数；
>
> c对象是一个全局对象，d是一个静态对象，都存在全局静态区，先创建的后销毁，所以先调用D类析构，最后调用C类析构。



第五题

定义一个学生类，其中有3个数据成员：学号、姓名、年龄，以及若干成员函数。

同时编写main函数，使用这个类，实现对学生数据的赋值和输出。

``` c++
class Student{
public:
    Student(int id, const char * name, int age)
    : _id(id)
    , _name(new char[strlen(name) + 1]())
    , _age(age)
    {
        strcpy(_name,name);
        cout << "Student(int,const char*,int)" << endl;
    }

    Student(const Student & rhs)
    : _id(rhs._id)
    , _name(new char[strlen(rhs._name) + 1]())
    , _age(rhs._age)
    { cout << "Student(const Student &)" << endl; }

    Student& operator=(const Student & rhs)
    {   
        if(this != &rhs){
            cout << "Student& operator=(const Student&)" << endl;
            delete [] _name;
            _name = new char[strlen(rhs._name) + 1]();
            strcpy(_name,rhs._name);
            _id = rhs._id;
            _age = rhs._age;
        }
        return *this;
    }
    
    void print() const{
        cout << "id:" << _id << endl;
        cout << "name:" << _name << endl;
        cout << "age:" << _age << endl;
    }
    
    ~Student(){
        if(_name){
            delete [] _name;
            _name = nullptr;
        }
        cout << "~Student()" << endl;
    }

private:
    int _id;
    char * _name;
    int _age;
};
```





















