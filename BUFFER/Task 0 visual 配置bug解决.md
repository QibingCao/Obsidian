---

类型: 笔记
创建日期: 2024-07-20
修改日期: 2024-07-20
---
在最开始做minitorch项目的时候，我的电脑安装的是上形式化课时安装的python3.12.0，因为课上要用最新版本的新特性。
minitorch官方要求python版本要大于3.8，但是经过实测，版本太高项目中的有些包会版本不兼容，所以我又安装了一个python3.8.8版本。
由于我平时也不怎么写python，环境很干净，所以直接在base环境中安装了，没有按照官网上搞虚拟环境。

问题：
在按照教程执行`streamlit run app.py -- 0`时，会报错，具体报错是找不到minitorch这个包

很奇怪，因为在配置环境的时候就执行过命令`python -m pip install -Ue .`将整个Mudule 0项目打包成minitorch包了，而且我在项目根目录下python import minitorch没有问题，这我就更迷惑了。
多次尝试发现在根目录下确实没问题，但是在执行`streamlit`指令的`./project`子文件下就是不行
```shell
PS E:\minitorch\minitorch-module-0-QibingCao> python
Python 3.8.8 (tags/v3.8.8:024d805, Feb 19 2021, 13:18:16) [MSC v.1928 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import minitorch
>>> exit()
PS E:\minitorch\minitorch-module-0-QibingCao> cd .\project\
PS E:\minitorch\minitorch-module-0-QibingCao\project> python
Python 3.8.8 (tags/v3.8.8:024d805, Feb 19 2021, 13:18:16) [MSC v.1928 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import minitorch
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'minitorch'
```

查询后发现确实会出现这种情况，应该是在在执行`./project`的脚本时，当前目录下并没有minitorch这个包

于是我
```
import sys 
sys.path.append('..') # 添加父目录到 sys.path 
import minitorch
```
确实不报错了，但是又提示我别的import有问题，那我只能乖乖搞个虚拟环境。。。

## pycharm
pycharm中每创建一个新的module项目都可以直接在python iterpreter那将路径改成module 0的，这样就只用一个虚拟环境就行
