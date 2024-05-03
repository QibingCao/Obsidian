---

类型: 笔记
创建日期: 2024-05-04
修改日期: 2024-05-04
---
## 注意
我并未按照PA0的要求采用实体Ubuntu系统，而是用最新版支持GUI的wsl2 + Ubuntu22.04版本进行替代，途中可能会出现各种bug，希望微软的虚拟化做的真的不错。
按照教程更新并下载了wsl关于GUI的一些使用软件：
[教程](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps)
## make的使用
执行文件夹中的`Makefile`文件的内容
详细教程害得慢慢看

## gdb的使用
[PA推荐教程](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps)
教程教了基本的用法
**首先要让linux配置为core dump模式**，这样会生成中间文件，类似这样：
core.1341870.1000.8.test.out.159886771
### 进入gdb
`gdb ./test.out ./core.1341870.1000.8.test.out.1598867712`
### bt
back tracing
为我们提供了程序当前状态的跟踪（调用过程后的过程）。可以将其视为发生事情的逆序；帧 `#0` （第一帧）是程序崩溃时正在执行的最后一个函数，而帧 `#2` 是程序启动时调用的第一个帧
### 帧检查
```
(gdb) f 2 // 进入第二帧
#2  0x000055fa2323318a in main () at test.c:17
17    calc();
(gdb) list // 展示出上下文代码
12    actual_calc(a, b);
13    return 0;
14  }
15  
16  int main(){
17    calc();
18    return 0;
19  }
(gdb) p a // 打印变量a
No symbol "a" in current context.
```
