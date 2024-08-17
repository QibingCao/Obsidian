---

类型: 笔记
创建日期: 2024-08-11
修改日期: 2024-08-11
---
训练营最好的地方是氛围，其次是负责的老师们和班主任、每天配套的作业练习和阶段项目，还有就是泥鳅老师说的，课程的设计是掌握一个工作岗位所需的主干知识，省去了自学时会走的弯路，学一些跟岗位要求不相干的东西。

课程的内容有很好的补充，对于英语的阅读（必要）和听力（b站有字幕版可帮一把）还不错的道友，可以看一些国外的课程，贴一些很好的整理：[PKUflyingPig](https://csdiy.wiki/)，[胡津铭](https://conanhujinming.github.io/comments-for-awesome-courses/)（这两位也是我的精神导师）。除此以外，也可以选择很多优秀的国内课程。这里插句题外话，jyy的[计算机系统基础](https://nju-projectn.github.io/ics-pa-gitbook/ics2021/)【[视频](https://www.bilibili.com/video/av669652328)】和[操作系统](http://jyywiki.cn/OS/2021/)就感觉很不错（注：我只略看了ppt，加入了`下次一定`名单，且两者跟王道训练营内容并不直接相关，难度较大）。

我大概是从今年（2022）年初开始真正写代码的，对于我这种代码实践经验很少，只死记硬背过408的人而言，[cs50x](https://cs50.harvard.edu/x/2022/)是很好的计算机入门导论课程，必推，课程最后的`finance`项目对于理解和完成王道的c学生管理系统项目，linux的百度网盘项目，还有学习HTTP阶段的内容，都很有帮助。其次就是[misssing semester](https://missing-semester-cn.github.io/)，有点像计算机工具导论课，强推，大部分是关于linux的内容，虽然在训练营的前一两个月还不需要用linux，但还是建议能越早了解越好。我个人的学习体会是刚上来不要要求自己都能吃下来，而是了解主干框架，把能用到的工具给装上，回头遇到相关方面的问题再过来查找。

## C阶段
在C语言阶段，我学习了[cse251](https://www.cse.msu.edu/~cse251/index.html)简易C语言入门课程，前半部分作业和全部的讲义ppt都可以看看，但后半部分的作业和项目需要2014年的环境才能配置的图形库，不是很推荐。在PKUflyingPig的整理中有了新的[C语言课程](https://www.coursera.org/specializations/c-programming)选择（不是免费的了），以后有机会或者我能从新来过的话，我应该会选择这个课程。

## Linux阶段：
  - 之前提到的missing semseter可以在这时候边用边学，有了实际的应用，理解起来会很快
  - 《TCP IP网络编程》（韩）尹圣雨（王道服务器里有）
  - 进程池和线程池参考：[小林coding-图解系统-9.网络系统](https://xiaolincoding.com/os/8_network_system/zero_copy.html)
  - sql入门推荐搭配w3school,里面有[keyword reference](https://www.w3schools.com/sql/sql_ref_keywords.asp)还有[配套练习](https://www.w3schools.com/sql/sql_exercises.asp)

## C++阶段：
c++的资料非常得多，经典书单不再赘述，这里补充一些自己的体会。大的原则就是挑着看，带着问题看，想全吃下来，没时间的。

必看：
  - [hacking C++](https://hackingcpp.com/index.html)系统全面的c++学习网站，整合了很多资源，很多直观的图，新手友好（[Beginner's Guide](https://hackingcpp.com/cpp/beginners_guide.html#intro)）

能抓住modern C++(C++17)主干，方便入门的：
  - [cs106L](https://web.stanford.edu/class/cs106l/)【[视频](https://www.bilibili.com/video/BV1yJ411m7CE)】斯坦福modern C++(C++17)课程
  - 【[快速学习C和C++，基础语法和优化策略(南科大计算机系)](https://www.bilibili.com/video/BV1Vf4y1P7pq?p=63)】
  - [《](https://changkun.de/modern-cpp/zh-cn/00-preface/)[现代 C++ 教程: 高速上手 C++ 11/14/17/20](https://changkun.de/modern-cpp/zh-cn/00-preface/)》正如书名所说，精简的笔记

进一步的学习资料：
  - [候捷系列网课](https://pan.baidu.com/s/1xEgRRKaHFaVOtXx0BbmZhw?pwd=wd43)（提取码:wd43），自己根据王道课程内容看过一部分stl和内存管理的课，讲得非常好，以后有时间一定会刷完
  - [【录播】现代C++中的高性能并行编程与优化（持续更新中）](https://www.bilibili.com/video/BV1fa411r7zp?vd_source=8fbe66e7c96f76c12929e16b0eed07a6)

      不必全看，但要做相应的作业，做完后记得看看优秀作业，推荐的内容：

      - 第一讲的作业中cmake的练习和github的应用，对了解这两块对后面写项目构建系统和合作有帮助
      - 第二讲：RAII、智能指针、三五原则（rule of 5/0），个人感觉是C++重要的基础内容

      其他的内容就看自己的兴趣和余力了
  - 《[C++ Core Guidelines》(2022)](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
  - [~~cpp-fundamentals-for-professionals~~](https://www.educative.io/courses/cpp-fundamentals-for-professionals)~~ （C++17）很好的讲义，很多可以在网页上运行的代码例子和小练习~~

  - 推荐文章合集
    - [RAII](https://hackingcpp.com/cpp/std/unique_ownership.html)
    - [move semantic](https://hackingcpp.com/cpp/lang/move_semantics.html#hfold0a)
        - [Advantages of pass-by-value and std::move over pass-by-reference ](https://stackoverflow.com/questions/51705967/advantages-of-pass-by-value-and-stdmove-over-pass-by-reference)
        - [什么是move？理解C++ Value categories，move， move in Rust](https://zhuanlan.zhihu.com/p/374392832)
        - [C++: 再谈move，右值引用的由来 ](https://zhuanlan.zhihu.com/p/515808385)

  - 我（菜鸟）关于modern C++（C++11/14/17/20）的经验谈
    - 在modern c++中，`singleton`设计模式不推荐，应该尽量使用`Dependency injection`来替代，如果想用`singleton`，c++11之后的实现贴在[这里](https://www.zhihu.com/question/333710483/answer/2297774354)
    - （2022.5）在clang和gcc上使用c++20 `module`特性很麻烦，`intellisense` 没有很好的支持，建议跳过这个部分



---

一些零散的建议：

- 在开营装linux系统时，一开始先换源然后再参照王道文档去装，具体参照[ubuntu20.04换清华源](https://blog.csdn.net/qq_23835707/article/details/111629344)，其他`ubuntu`的版本自行google
- ubuntu默认编辑器是`nano`，在`~/.bashrc`（用zsh的话是`~/.zshrc`）中添加`export EDITOR='vim'`可以将默认编辑器改为vim
- 【[使用 VS Code + Clangd + CMake 搭建 C/C++开发环境](https://www.bilibili.com/video/BV1sW411v7VZ?p=6)】
- [【GDB调试教程】](https://www.bilibili.com/video/BV1kP4y1K7Eo)，评论区有快查表和补充视频，记得翻一翻，也欢迎查阅[我的笔记](https://www.wolai.com/myboy/t9VFbutphNuX4YAW3m37mw)
- [cmake](https://www.wolai.com/myboy/mHKaLxtTCDBu24qfmUJpHB)是c++事实上的[构建标准](https://hackingcpp.com/cpp/tools/build_systems.html)，[xmake](https://www.wolai.com/ftXPfQzG8ZXiyfqm3qs9Qd)上手简单，可以都学:)
- 我对[单元测试](https://mp.weixin.qq.com/s/qNxTu3ql0V6YZD677maMCA)/测试驱动开发(TDD)很感兴趣，我的学习路径是看书和教程掌握基本用法，再通过国外课程中(cs61a, cs61b，CMU 15-445)的测试例子来学习迁移到c++中来。
    - 一直想学，绕了很多弯路。
    - [c++的单元测试框架](https://hackingcpp.com/cpp/tools/testing_frameworks.html)：`doctest` 、`gtest`
    - TDD好的练习例子：
        - Josh Lospinoso(2019). *C++ crash course : a fast-paced introduction* 的第10章
        - [Project #0 - C++ Primer](https://15445.courses.cs.cmu.edu/fall2020/project0/)，写完lab后，学习下整个项目的框架，帮助很大。
    - 为什么没有c的部分呢？因为我在C阶段花了很长时间都没搞懂单元测试是咋回事...

- 大佬的推荐学习方法

  ![](https://secure2.wostatic.cn/static/rHXASweEkLScvkvt1VpZ4i/image.png?auth_key=1723389940-sxE8AmN7nWkYzrnMg7oGa6-0-6c41c25695251db658f1c68be6a7679a)



- 尾巴

  最后，来了跑步训练营，推一部跑步番《[强风吹拂](https://movie.douban.com/subject/30238385/)》

  “认为努力就能做到最好是种奢侈的傲慢，明知无法登顶却还是要拼尽全力向更好的自己奔跑。”——《强风吹拂》豆瓣短评

