# 第一题   词频统计（vector）

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

<span style=color:red;background:yellow>**见day12/test/01.DictVector.cc**</span>

# 第二题  词频统计（map）

词频统计的作业再用map容器去实现一次，体验一下使用vector/map时程序执行的速度

提示：++dict[word];

<span style=color:red;background:yellow>**见day12/test/01.DictMap.cc**</span>

# 第三题 文本查询

文本查询
该程序将读取用户指定的任意文本文件【当前目录下的china_daily.txt】，然后允许用户从该文件中查找单词。查询的结果是该单词出现的次数，并列出每次出现所在的行。如果某单词在同一行中多次出现，程序将只显示该行一次。行号按升序显示。

要求：
a. 它必须允许用户指明要处理的文件名字。

b. 程序将存储该文件的内容，以便输出每个单词所在的原始行。
vector<string> lines;//O(1)

c. 它必须将每一行分解为各个单词，并记录每个单词所在的所有行。
在输出行号时，应保证以升序输出，并且不重复。

map<string, set<int> > wordNumbers;
map<string, int> dict;

d. 对特定单词的查询将返回出现该单词的所有行的行号。

e. 输出某单词所在的行文本时，程序必须能根据给定的行号从输入
文件中获取相应的行。

示例：
使用提供的文件内容，然后查找单词 "element"。输出的前几行为：
\---------------------------------------------
element occurs 125 times.
(line 62) element with a given key.
(line 64) second element with the same key.
(line 153) element |==| operator.
(line 250) the element type.
(line 398) corresponding element.
\---------------------------------------------

程序接口[可选]:



``` c++
class TextQuery
{
public:
//......
void readFile(const string filename);
void query(const string & word);//

private:
//......
//_lines存的是一行一行的内容
vector<string> _lines;//O(1)
map<string, set<int> > _wordNumbers;//the the
map<string, int> _dict;//
};


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

<span style=color:red;background:yellow>**见day12/test/03.TextQuery.cc**</span>





