

第一题

**什么是赋值运算符函数，其形态是什么？什么情况下需要手动提供赋值运算符函数呢？**

> 当对象间进行赋值操作时（将一个已存在的对象的内容复制给另一个已存在的对象），编译器会自动提供一个默认的赋值运算符函数。
>
> 由于内置类型的赋值操作都是通过 = 号进行，自定义类型对象与内置类型保持一致，也使用 = 号进行赋值。编译器在处理时实际是把 = 当成了一次函数调用，调用了赋值运算符函数（函数名为operator=），其形态为：
>
> ``` c++
> 类名 & operator=(const 类名 & 形参){
>     //...
> }
> ```
>
> 
>
> 当类中有指针数据成员，在创建对象时申请了堆空间资源时，需要显示定义赋值运算符函数。





第二题

**this指针是什么? 有什么作用呢？**

> this指针的本质是一个指针常量 Type* const pointer; 它储存了调用它的对象的地址，不可被修改。这样成员函数才知道自己修改的成员变量是哪个对象的。
>
> this是一个隐藏的指针，可以在类的成员函数中使用，它可以用来指向调用对象。当一个对象的成员函数被调用时，编译器会隐式地传递该对象的地址作为 this 指针。
>
> <span style=color:red;background:yellow>**this指针指向本对象**</span>





第三题

写出以下程序运行的结果

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

> obj1和obj2的创建分别打印1/2
>
> obj3的创建调用了拷贝构造函数，打印2
>
> 最后这三个对象都会被销毁，打印3次4







第四题

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

> 调用func函数输出B func(const B &)
>
> 执行return语句时触发拷贝构造函数的第三种调用时机（函数返回一个对象），输出B(const B&)
>
> 然后对b2赋值，调用赋值运算符函数，输出B &operator=(const B &s)







第五题

实现上课时候的单例模式的代码，试一试补充功能，能够修改堆上单例对象的内容。

<span style=color:red;background:yellow>**见day05/01.Singleton.cc**</span>









