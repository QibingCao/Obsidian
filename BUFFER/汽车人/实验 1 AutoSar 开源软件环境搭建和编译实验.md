---

类型: 笔记
创建日期: 2024-06-04
修改日期: 2024-06-04
---
## 实验目的：

了解 AutoSar 软件结构，掌握 Linux 平台下 Autosar 环境的搭建和开源代码编译。通过搭建过程熟悉硬件模拟器 Qemu 和 Scons 命令的使用。
## 实验内容：
1. Linux 虚拟机配置
2. AutoSar 开源软件下载
3. Qemu 的运行与使用
4. 基于 Scons 编译 AutoSar 源码
## AS代码结构
```
joseph@ubuntu:~/as$ tree -L 2
.
├── autorun.sh                      # 自动运行脚本，用于启动化任务
|
├── build                           # 编译输出目录
│   └── posix                       # POSIX环境下的编译输出文件
|
├── com                             # 通信相关代码目录
│   ├── as.application              # 应用层代码
│   ├── as.infrastructure           # 基础设施代码
│   ├── as.tool                     # 工具代码
│   ├── as.virtual                  # 虚拟化相关代码
│   └── SConscript                  # 用于SCons构建系统的脚本文件，定义了构建过程
|
├── Console.bat                     # Windows下的控制台批处理文件，用于执行特定任务
├── i686-elf-gcc -> /usr/bin/gcc    # GCC编译器的符号链接，用于交叉编译i686 ELF文件
├── i686-elf-ld -> /usr/bin/ld      # 链接器的符号链接，用于链接i686 ELF文件
├── Kconfig                         # 配置文件，用于配置编译选项和特性
├── kernel.bin                      # 编译生成的内核二进制文件
├── README.md                       # 项目的说明文档，包含项目的基本信息和使用说明
├── release                         # 发布版本目录
│   ├── asboot                      # 引导程序代码
│   ├── ascore                      # 核心代码
│   ├── askar                       # 仿真器代码
│   ├── aslinux                     # Linux相关代码
│   ├── asminix                     # Minix相关代码
│   ├── asslave                     # 从属设备代码
│   ├── download                    # 下载目录
│   ├── README.md                   # 发布版本的说明文档
│   ├── SConscript                  # 用于SCons构建系统的脚本文件，定义了构建过程
│   └── ucos_ii                     # UCOS-II操作系统相关代码
|
├── SConscript                      # 用于SCons构建系统的脚本文件，定义了构建过程
├── SConstruct                      # SCons构建系统的主脚本文件，定义了顶层构建过程
|
├── socket_tool                     # 套接字工具目录
│   ├── client                      # 客户端工具目录
│   ├── client.c                    # 客户端程序的C源代码
│   ├── doip.c                      # DOIP协议的C源代码
│   ├── doip.h                      # DOIP协议的头文件
│   ├── makefile                    # Makefile文件，用于构建套接字工具
│   ├── send_client.sh              # 用于发送客户端的脚本
│   ├── server                      # 服务器工具目录
│   ├── server.c                    # 服务器程序的C源代码
│   ├── TCP_Client_AAAA.py          # TCP客户端的Python源代码
│   └── UDP_Client.py               # UDP客户端的Python源代码
|
└── TINIX.IMG                       # TINIX操作系统的镜像文件

17 directories, 23 files
```
为了达到代码复用的目的，AUTOSAR的结构是分层的，具体可以分为三大层：应用软件层（AppL）、实时运行环境（RTE）、基础软件层（BSW）。在此系统中均在`./as/com`文件夹下。
### SWC（软件组件）
代码路径：`as/com/as.application/common/test`
应用软件层（ASW）包含多个软件组件（SWC），通过端口进行交互。软件组件工作在运行时工作环境中。
### RTE（运行时环境）
代码路径：`as/com/as.application/common/rte`
RTE充当应用软件层与基础软件层的桥梁，提供标准化的接口，实现软硬件分离。当SWC变化时，RTE确保基础软件不受影响，通过统一的接口函数调用服务，实现ECU间的通信。
### BSW（基础软件层）
代码路径：`as/com/as.infrastructure`
基础软件层分为四层：服务层、ECU抽象层、微控制器抽象层（MCAL）和复杂驱动。服务层提供系统服务、存储器服务和通信服务。ECU抽象层通过统一接口访问通信、存储和I/O设备。MCAL封装硬件接口，避免上层软件直接操作微控制器寄存器。复杂驱动层处理复杂传感器和执行器的时序问题。
![[Pasted image 20240611211129.png]]
AUTOSAR通过分层架构实现了灵活的软硬件分离，确保系统的模块化和可扩展性。
## 编译
此系统采用Scons脚本来进行编译，具体的，Scons会使用封装过的python语言来对源文件进行操作。
执行`scons`命令时，首先会执行根目录下的`as/SConstruct`文件，其主要流程如下：
1. `PrepareEnv`：准备环境变量。
2. `SConscript`：加载附属的配置文件。
3. `building`：编译过程。

在`SConstruct`中，首先添加了环境变量：
```python
studio = os.path.abspath('./com/as.tool/config.infrastructure.system/') 
sys.path.append(studio) 
from building import * 
asenv = PrepareEnv()
```

`PrepareEnv`函数在`building.py`中定义，用于构建环境并添加必要的变量。
```python
asenv = Environment(TOOLS=['ar', 'as', 'gcc', 'g++', 'gnulink']) 
os.environ['ASROOT'] = ASROOT 
asenv['ASROOT'] = ASROOT
```
环境变量`BOARD`用于指定目标板：
```python
BOARD = os.getenv('BOARD')
if BOARD not in board_list:
    print('Error: no BOARD specified!')
    help()
    exit(-1)

```
加载配置文件：
```python
objs = SConscript('SConscript', variant_dir=BDIR, duplicate=0)
Building(target, objs)

```
`Building`函数在`building.py`中定义，用于分类处理需要编译的文件，并生成目标文件。
#### SConscript生成objs
根目录下的`SConscript`文件负责将需要编译的文件加入到`objs`集合中：
```python
from building import * 
objs = SConscript('com/SConscript') 
objs += SConscript('release/SConscript') 
Return('objs')

```

#### arxml生成LCfg文件
`arxml`文件用于生成配置文件，如`asmconfig.h`。以下是生成配置文件的过程：
```python
MKDir(cfgdir)
RMFile(cfgdone)
xcc.XCC(cfgdir, env, True)
if arxml:
    arxmlR = PreProcess(cfgdir, str(arxml))
    for xml in xmls:
        MKSymlink(str(xml), '%s/%s' % (cfgdir, os.path.basename(str(xml))))
    xcc.XCC(cfgdir)
    argen.ArGen.ArGenMain(arxmlR, cfgdir)
MKFile(cfgdone, SHA256(glob.glob('%s/*xml' % (cfgdir))))
```
上述代码通过调用`PreProcess`和`ArGenMain`函数，生成必要的配置文件和代码。

#### DTS、OFS、SWCS编译
编译DTS、OFS和SWCS文件：
```python
BuildDTS(dts, BDIR)
BuildOFS(ofs)
BuildingSWCS(swcs)
```
这些步骤用于处理特定的文件类型，并生成相应的中间代码和目标文件。

#### 生成TINIX.IMG
最后一步是生成`TINIX.IMG`镜像文件，并将编译生成的二进制文件复制到镜像文件中。
## 实验结果
`scons run`结果如下，Qemu窗口为模拟的硬件，AS代码在此虚拟机之上运行。
![[Pasted image 20240605004703.png]]
在telnet界面中可执行相应的操作。
![[Pasted image 20240611204651.png]]
![[Pasted image 20240611204721.png]]