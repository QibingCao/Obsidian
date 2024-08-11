第一题

**什么是友元？友元的存在形式有？友元有何特点？**

> 一般来说，类的私有成员只能在类的内部访问，类之外是不能访问它们的。但如果将其他类/函数设置为类的友元，那么友元类/函数就可以在前一个类的类定义之外访问其私有成员了。<span style=color:red;background:yellow>**用friend关键字声明友元**</span>。

> 友元的三种形式：**普通函数、成员函数、友元类**

> 友元的特点：
>
> 1. **友元不受类中访问权限的限制**——可访问私有成员
> 2. **友元破坏了类的封装性**
> 3. **不能滥用友元 ，友元的使用受到限制**
> 4. **友元是单向的**——A类是B类的友元类，则A类成员函数中可以访问B类私有成员；但并不代表B类是A类的友元类，如果A类中没有声明B类为友元类，此时B类的成员函数中并不能访问A类私有成员
> 5. **友元不具备传递性**——A是B的友元类，B是C的友元类，无法推断出A是C的友元类
> 6. **友元不能被继承**——因为友元破坏了类的封装性，为了降低影响，设计层面上友元不能被继承
>





第二题

**自增运算符的前置形式和后置形式有什么区别？返回值类型分别是什么？哪一种形式的效率更高呢？**

``` c++
	//前置++
    Complex & operator++(){
        cout << "operator++()" << endl;
        ++_real;
        ++_image;
        return *this;
    }

    //后置++运算符重载函数的参数列表中加入一个int
    //与前置++进行区分
    //返回类型是对象，不是引用
    Complex operator++(int){
        cout << "operator++(int)" << endl;
        Complex temp(*this);
        ++_real;
        ++_image;
        return temp;
    }
```

> 后置++的运算符重载函数在参数列表中加上一个int；
>
> 前置++返回对象本身，后置++返回对象改变前的副本；
>
> 前置++减少了一次复制操作，效率更高。







第三题

编写Base类使下列代码输出为1

```cpp
int i=2;
int j=7;

Base x(i);
Base y(j);
cout << (x+y == j - i) << endl;
```

提示：本题考查的其实就是运算符重载的知识点。



``` c++
class Base{
public:
    Base(int val)
    :_val(val)
    {}
    
    friend 
    int operator+(const Base &lhs,const Base &rhs);
private:
    int _val;
};

int operator+(const Base &lhs,const Base &rhs) //重载+运算符
{
    return (lhs._val-rhs._val);
}

void test1()
{
    int i = 2;
    int j = 7;
    Base x(i);
    Base y(j);
    cout << (x + y == j - i) << endl;
}

int main()
{
    test1();
    return 0;
}
```







第四题

使用log4cpp格式化输出的信息，同时要求输出到终端、文件和回卷文件中。

提示：通过实践来感受log4cpp的使用，这是学习任何第三方库的第一步要做的事儿，先从模仿开始，再慢慢逐步理解。学会使用第三方库，是工作中的基本能力，一定要掌握方法论。

<span style=color:red;background:yellow>**见day07/06.log4cppRollingFile.cc**</span>



第五题

用所学过的类和对象的知识，封装log4cpp，让其使用起来更方便，要求：可以像printf一样，同时输出的日志信息中最好能有文件的名字，函数的名字及其所在的行号(这个在C/C++里面有对应的宏，可以查一下)

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

<span style=color:red;background:yellow>**见day09/Mylogger/**</span>











