---

类型: 随想
创建日期: 2024-05-03
修改日期: 2024-05-03
---
[我的参考链接](https://zhuanlan.zhihu.com/p/533852291)
[牛逼学弟的链接](https://vgalaxy.work/posts/gitlet/)

最后auto grader分数如下，除了涉及到remote的选做项目，分数都拿到了。编码格式被扣分，总结以下扣分点：
- 函数体不得超过80行
- 模板<>括号是否要留空格
- 不允许捕获异常
![[Pasted image 20240503171916.png]]
### 实验感受
总的来说，实验手册算是比较详细。手册能覆盖80%的功能设计上的问题，但是剩下的20%既没在文档之中也不在官方的习题课视频中提到过。在最后我是靠知乎上的帖子给出的autograder的测试样例，来反推一些函数的定义问题。如果没有扒下来的样例，我要debug的时间会被大大延长。
## 项目设计说明
### 整体架构
```
.gitlet
├── WorkSpace
├── Commits
│   ├── ac2ft32ge02...
│   ├── ...
├── blobs
│   ├── asda3qv1vac...
│   └── ...
```
所有文件放在`.gitlet`文件夹中。
`WorkSpace`中保存当前git的工作状态，变量如下：
```java
private TreeMap<String, Commit> branches; // git分支-->分支指向的那个Commit
private String currentBranch; // 当前分支名
private TreeMap<String, String> stagedFiles; // 文件名-->blob
private TreeMap<String, String> removedFiles; // 文件名-->blob

// 未实现，暂时不用
private TreeMap<String, String> modificationsNotStagedForCommit;
private TreeSet<String> untrackedFiles;
```

`Commits`中存放所有以SHA-1为文件名的Commit，Commit定义如下：
```java
 /** parents of this Commit. */
    private List<String> parents;
    
    /** all ancestors of this Commit. */
    private TreeSet<String> ancestors;
    
    /** The hashcode of this Commit, aka UID*/
    private String hashCode;

    /** The current time of this Commit.*/
    private Date date;

    /** The message of this Commit. */
    private String message;
    
	/** The blobs of this Commit */
    private TreeMap<String, String> blobs ;
```
