第一题  

**什么是C风格的字符串？什么是C++风格的字符串？说说你熟悉的C++风格字符串的常用操作，各操作的功能是什么？**

>  C 风格字符串是以 '\0' （空字符）来结尾的字符数组，在C++中通常用const char * 表示，用“ ”包括的认为是C风格字符串。
>
> std::string标准库提供的一个自定义类类型basic_string，string 类是 basic_string 类模板关于 char 型的实例化。使用起来不需要关心内存，直接使用即可。

> string的常用操作
>
> 
>
> ``` c++
> const CharT* data() const;
> const CHarT* c_str() const; //C++字符串转为C字符串
> 
> bool empty() const; //判空
> 
> size_type size() const;//获取字符数
> size_type length() const;
> 
> void push_back(CharT ch);  //字符串结尾追加字符
> 
> //在字符串的末尾添加内容，返回修改后的字符串
> basic_string& append(size_type count, CharT ch); //添加count个字符
> basic_string& append(const basic_string& str);  //添加字符串
> basic_string & append(const basic_string& str,  //从原字符串的pos位置，添加字符串的count个字符
>                      size_type pos,size_type count);
> basic_string& append(const charT* s);      //添加C风格字符串
> 
> //查找子串
> size_type find( const basic_string& str, size_type pos = 0 ) const;  //从C++字符串的pos位开始查找C++字符串
> size_type find( CharT ch, size_type pos = 0 ) const;      //从C++字符串的pos位开始查找字符ch
> size_type find( const CharT* s, size_type pos, size_type count ) const;  //从C++字符串的pos位开始，去查找C字符串的前count个字符
> ```
>
> 





第二题 

**实现一个类只能生成堆对象，不能生成栈对象的代码，并能做到正常回收该堆对象。**

<span style=color:red;background:yellow>**见day06/test/01.HeapStudent.cc**</span>

析构函数私有，同时提供destroy函数回收堆对象

<img src="https://bray07.oss-cn-beijing.aliyuncs.com/undefined202403251834753.png" alt="image-20240325183454730" style="zoom:67%;" />





第三题

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

<span style=color:red;background:yellow>**见day06/test/02.String.cc**</span>



第四题

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

<span style=color:red;background:yellow>**见day06/test/03.Stack.cc**</span>









