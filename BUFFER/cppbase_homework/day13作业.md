第一题 

构建一个类Person，包含字符串成员name（姓名），整型数据成员age（年龄），成员函数 display()用来输出name和age。构造函数包含两个参数，用来对name和age初始化。构建一个类Employee由Person派生，包含department（部门），实型数据成员salary（工资）,成员函数display（）用来输出职工姓名、年龄、部门、工资，其他成员根据需要自己设定。主函数中定义3个Employee类对象，内容自己设定，将其姓名、年龄、部门、工资输出。

要求：用char*来保存字符串内容，并能实现Employee对象的复制、赋值操作。



``` c++
    Employee(const Employee & rhs)
    : Person(rhs)  //显示调用基类的拷贝构造
    , _department(new char [strlen(rhs._department) + 1]())
    , _salary(rhs._salary)
    {
        strcpy(_department, rhs._department);
        cout << "Employee(const Employee &)" << endl;
    }

    Employee & operator=(const Employee & rhs){
        if(this != &rhs){
            Person::operator=(rhs); //显示调用基类的赋值运算符函数
            delete [] _department;
            _department = new char[strlen(rhs._department) + 1]();
            strcpy(_department, rhs._department);
        }
        return *this;
    }
```







第二题

**C++中有哪几种多态？请详细说明一下**

静态多态：函数重载、运算符重载

动态多态：虚函数机制。当类中定义了虚函数之后，就会在对象的存储开始位置，多一个虚函数指针，该虚函数指针指向一张虚函数表，虚函数表中存储的是虚函数入口地址。如果此类往下派生，派生类中对虚函数进行了覆盖，对于派生类而言，将会覆盖掉虚函数表中虚函数的入口地址。

当一个基类指针指向派生类对象，并用这个指针来调用此虚函数，可以通过虚函数指针，找到虚函数表，由于虚函数表中记录的已经是派生类实现的虚函数，所以能够调用到派生类覆盖后的虚函数。基类的指针共享了派生类的方法，这就是虚函数（动态多态）的机制。

<img src="https://bray07.oss-cn-beijing.aliyuncs.com/undefined202403200905151.png" alt="image-20240320090529074" style="zoom:67%;" />





第三题

**在什么情况下析构函数要设置成虚函数？为什么？**

<span style=color:red;background:yellow>**在实际的使用中，如果有通过基类指针回收派生类对象的需求，都要将基类的析构函数设为虚函数。**</span>

建议：一个类定义了虚函数，就将它的析构函数设为虚函数。

如果析构函数没有设置为虚函数，那么在通过这个基类指针回收派生类对象时（delete pbase），会首先尝试调用派生类的析构函数，而基类指针无法访问到派生类的析构函数。



第四题

**什么是纯虚函数？什么是抽象类？抽象类的作用是什么？**

**纯虚函数**是一种特殊的虚函数，在许多情况下，在基类中不能对虚函数给出有意义的实现，而把它声明为纯虚函数，**它的实现留给该基类的派生类去做**。这就是纯虚函数的作用。纯虚函数的格式如下：



``` c++
class 类名 {
public:
	virtual 返回类型 函数名(参数 ...) = 0;
};
```



抽象类有两种形式：

1 . 定义了纯虚函数的类，称为抽象类

2 . 只定义了protected型构造函数的类，也称为抽象类



抽象类的主要作用是对多个子类相同部分抽象为一个基类，其中相同的方法或数据在基类定义，如需更改的方法可在基类声明为纯虚函数，子类可自定义纯虚函数功能。**可理解为基类定义了类方法规范，具体功能由子类实现。**





第五题

写出结果

``` c++
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
    ptr1->fun();     //--Base1--
    
    ptr2=&obj2;         	
    ptr2->fun();    //--Base2--  
    
    ptr1=&obj3;         	
    ptr1->fun();    //--Derived--，覆盖
    
    ptr2=&obj3;         	
    ptr2->fun();    //--Base2--
    
    return 0;	                  

}
```





第六题

写出结果

``` c++
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

//FuncA called
//FuncBB called
//FuncA called
//FuncB called

```





第七题

写出结果

``` c++
class Base
{
public:
    Base(int j)
    : i(j) 
    {}
    virtual  ~Base() 
    {} 
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
    {}
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

//12
```





