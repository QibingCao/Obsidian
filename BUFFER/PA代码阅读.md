---

类型: 笔记
创建日期: 2024-05-05
修改日期: 2024-05-05
---
## 配置gdb
了解了gdb的基本用法，比如在程序内设置断点，单步执行等。在make完nemu后，发现无法给编译后的二进制文件添加断点，因为nemu没有调试文件。一番搜索过后，发现在Makefile中设置编译是并未加入-g等生成调试信息的关键字。还不咋会Makefile，所以第一时间没找到怎么去修改编译设置。
在加入-g之后vscode的gdb插件就能正常使用了，但是还是会出现局部变量由于被优化，显示不出来的情况。
思考：gdb的对象是一个可执行的二进制机器码文件，我们在编译时保留下一些代码的信息来供gdb在执行机器码时能反过来判断源c程序。
### 修改build.mk
此文件中设定了编译时的各种设置。
原设置：
```makefile
# Compilation flags
ifeq ($(CC),clang)
CXX := clang++
else
CXX := g++
endif
LD := $(CXX)
INCLUDES = $(addprefix -I, $(INC_PATH))
CFLAGS  := -O2 -MMD -Wall -Werror $(INCLUDES) $(CFLAGS)
LDFLAGS := -O2 $(LDFLAGS)
```
可以看出原设置不包含gdb需要的-g，并且优化等级为o2.
对此进行修改
修改后：
```
# Compilation flags
ifeq ($(CC),clang)
CXX := clang++
else
CXX := g++
endif
LD := $(CXX)
INCLUDES = $(addprefix -I, $(INC_PATH))
CFLAGS  := -O0 -g3 -MMD -Wall -Werror $(INCLUDES) $(CFLAGS)
LDFLAGS := -O0 -g3 -rdynamic $(LDFLAGS)
```
- **优化级别**：`-O0`确保无优化发生，以免影响调试。
- **调试级别**：`-g3`添加最详细的调试信息。
- **链接器标志**：`-rdynamic`保留符号信息。
### RTFSC
在看PA1的RTFSC时发现PA居然自带了调试用的设置，需要自己打开？
只需要在命令行中敲make menuconfig
。。。
## 基础设施

