第一题 

**C++中函数对象、成员函数指针该如何使用呢？请用代码给出示例。**

> 函数对象：当一个类定义了函数调用运算符重载函数，这个类的对象就称为函数对象，可以像函数一样去调用
>
> 
>
> ``` c++
> class FunctionObject{
> public:
>     //第一个括号表示运算符（函数调用运算符）
>     //第二个括号表示参数列表（无参形式）
>     int operator()(){
>         cout << "int operator()()" << endl;
>         ++_cnt;
>         return 1;
>     }
> 
>     //可以定义多种函数调用运算符重载函数
>     void operator()(int x){
>         cout << "void operator(int)" << endl;
>         ++_cnt;
>         cout << x << endl;
>     }
> 
>     int _cnt = 0;
> };
> 
> void test0(){
>     FunctionObject fo; 
>     
>     cout << fo() << endl;  //让对象像一个函数一样被调用
>     cout << fo.operator()() << endl; //本质
> 
>     fo(5);
>     fo.operator()(5);//本质
> }
> ```
>
> 



> 成员函数指针
>
> 定义一个成员函数指针需要确定的是这个成员函数的返回类型、参数类型，还需要确定是哪个类的成员函数（类的作用域）
>
> 与普通函数指针不一样的是，<font color=red>**成员函数指针的定义和使用都需要使用完整写法**</font>，不能使用省略写法，定义时要完整写出指针声明，使用时要完整写出解引用（解出成员函数后接受参数进行调用）。
>
> 另外，成员函数需要通过对象来调用，<font color=red>**成员函数指针也需要通过对象来调用**</font>。
>
> ``` c++
> class FFF{
> public:
>     void print(int x){
>         cout << "FFF::print:" << x << endl;
>     }
> 
>     void display(int x){
>         cout << "FFF::display:" << x << endl;
>     }
> };
> 
> //将此类的成员函数指针的类型定名为MemberFunc
> typedef void (FFF::*MemberFunc)(int);
> 
> void test3(){
>     //创建成员函数指针的时候就已经确定了
>     //返回类型、参数、是哪个类的成员函数
>     MemberFunc p = &FFF::print;
>     FFF fff;
>     //成员指针访问运算符的第一种形式
>     //指针指的是p，这是一个成员函数指针
>     (fff.*p)(10);
>     
>     p = &FFF::display;
> 	(fff.*p)(10);
>     
> 	//##################################
>     FFF * pff = new FFF();
>     //成员指针访问运算符的第二种形式
>     (pff->*p)(11);
> 
>     pff = nullptr;
>     (pff->*p)(11);
> }
> 
> 
> ```
>
> 





第二题

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



<span style=color:red;background:yellow>**见day10/test/StringOperator.cc**</span>



