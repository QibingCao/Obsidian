# 课题要求
在线实验平台的搭建
描述：当前在线教育及微课、慕课、翻转课堂成为教学实践探索的热点。本项目基于同学们自主开发的课程实验demo或实践项目，使用常见的前后端开发工具或框架、数据库MySQL、Mongodb等，实现作业提交、在线代码提交和调试结果展示、根据输出自动划定等次、题库积累和随机分配等功能。
本系统要求前后端分离，支持网络流媒体的快速录制和传输，易于检索，同时优化内存及硬盘资源占用。
## 项目介绍
OJ = Online Judge 在线判题评测系统
用户可以选择题目，在线做题，编写代码并且提交代码；系统会对用户提交的代码，根据我们出题人设置的答案，来判断用户的提交结果是否正确。
用于在线评测编程题目代码的系统，能够根据用户提交的代码、出题人预先设置的题目输入和输出用例，进行编译代码、运行代码、判断代码运行结果是否正确。

OJ 系统的常用概念
ac 表示你的题目通过，结果正确
题目限制：时间限制、内存限制
题目介绍
题目输入
题目输出
题目输入用例
题目输出用例

普通测评：管理员设置题目的输入和输出用例，比如我输入 1，你要输出 2 才是正确的；交给判题机去执行用户的代码，给用户的代码喂输入用例，比如 1，看用户程序的执行结果是否和标准答案的输出一致。
（比对用例文件）

特殊测评（SPJ）：管理员设置题目的输入和输出，比如我输入 1，用户的答案只要是 > 0 或 < 2 都是正确的；特判程序，不是通过对比用例文件是否一致这种死板的程序来检验，而是要专门根据这道题目写一个特殊的判断程序，程序接收题目的输入（1）、标准输出用例（2）、用户的结果（1.5） ，特判程序根据这些值来比较是否正确。

交互测评：让用户输入一个例子，就给一个输出结果，交互比较灵活，没办法通过简单的、死板的输入输出文件来搞定
不能让用户随便引入包、随便遍历、暴力破解，需要使用正确的算法。 => 安全性
判题过程是异步的 => 异步化
提交之后，会生成一个提交记录，有运行的结果以及运行信息（时间限制、内存限制）

现有系统调研
[https://github.com/HimitZH/HOJ](https://github.com/HimitZH/HOJ)（适合学习）
[https://github.com/QingdaoU/OnlineJudge](https://github.com/QingdaoU/OnlineJudge)（python，不好学，很成熟）
[https://github.com/hzxie/voj](https://github.com/hzxie/voj)（星星没那么多，没那么成熟，但相对好学）
[https://github.com/vfleaking/uoj](https://github.com/vfleaking/uoj)（php 实现的）
[https://github.com/zhblue/hustoj](https://github.com/zhblue/hustoj)（成熟，但是 php）
[https://github.com/hydro-dev/Hydro](https://github.com/hydro-dev/Hydro)（功能强大，Node.js 实现）

实现核心
1）权限校验
谁能提代码，谁不能提代码

2）代码沙箱（安全沙箱）
用户代码藏毒：写个木马文件、修改系统权限
沙箱：隔离的、安全的环境，用户的代码不会影响到沙箱之外的系统的运行

资源分配：系统的内存就 2 个 G，用户疯狂占用资源占满你的内存，其他人就用不了了。所以要限制用户程序的占用资源。

3）判题规则
题目用例的比对，结果的验证

4）任务调度
服务器资源有限，用户要排队，按照顺序去依次执行判题，而不是直接拒绝

核心业务流程
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1706325473780-002fab9d-ef20-48c2-b67b-d68fa8fc8df9.png#averageHue=%23fcfcfb&clientId=u019469ba-3bd7-4&from=paste&height=428&id=ud28db457&originHeight=481&originWidth=976&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=35651&status=done&style=none&taskId=u6875a0e0-d6d0-4ee7-a413-3d64d9492b0&title=&width=867.5555555555555)

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1706325484605-00b9e3a5-5798-4f7d-9c30-0d8f95ed472d.png#averageHue=%23f9f8f6&clientId=u019469ba-3bd7-4&from=paste&height=683&id=u5efc15e4&originHeight=768&originWidth=970&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=61251&status=done&style=none&taskId=uc24f54e1-d2ce-4ab1-8f73-fce420e9b57&title=&width=862.2222222222222)

判题服务：获取题目信息、预计的输入输出结果，返回给主业务后端：用户的答案是否正确
代码沙箱：只负责运行代码，给出结果，不管什么结果是正确的。

实现了解耦

功能
1题目模块
a创建题目（管理员）
b删除题目（管理员）
c修改题目（管理员）
d搜索题目（用户）
e在线做题
f提交题目代码
2用户模块
a注册
b登录
3判题模块
a提交判题（结果是否正确与错误）
b错误处理（内存溢出、安全性、超时）
c自主实现 代码沙箱（安全沙箱）
d开放接口（提供一个独立的新服务）

项目扩展思路
1支持多种语言
2Remote Judge
3完善的评测功能：普通测评、特殊测评、交互测评、在线自测、子任务分组评测、文件IO
4统计分析用户判题记录
5权限校验

技术选型
前后端全栈，所有都有，每行代码都是直播

前端：Vue3、Arco Design 组件库、手撸项目模板、在线代码编辑器、在线文档浏览
Java 进程控制、Java 安全管理器、部分 JVM 知识点
虚拟机（云服务器）、Docker（代码沙箱实现）
Spring Cloud 微服务 、消息队列、多种设计模式

架构设计
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1706325497392-a42dbc16-20cb-48c0-be40-47fe65afe587.png#averageHue=%23fdfdfd&clientId=u019469ba-3bd7-4&from=paste&height=802&id=u4610d3ad&originHeight=902&originWidth=956&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=39995&status=done&style=none&taskId=u97f9fd73-5d03-4a18-840d-10714c96d88&title=&width=849.7777777777778)

