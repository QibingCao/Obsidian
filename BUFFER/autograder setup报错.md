---

类型: 笔记
创建日期: 2024-07-21
修改日期: 2024-07-21
---
本地测试没有任何问题，但是在远程测试就会有不得分的情况
查看了测试的文件，发现测试应该是在虚拟机中跑项目，对比结果的方式来测试的。但是由于虚拟机的配置和本机的不同，很多包的行为都有差别。

别的都能接受，看着报错改就完事了。但是环境配置报错是没想到的
```
/usr/local/lib/python3.10/dist-packages/numpy/__init__.pyi:642: error: Positional-only parameters are only supported in Python 3.8 and greater

[24](https://github.com/minitorch/minitorch-module-1-QibingCao/actions/runs/10025134084/job/27707981209#step:3:25)Found 1 error in 1 file (errors prevented further checking)

```

于是我在yml文件里在后边加了几条查看环境的命令：先查看了python版本是3.10，没啥问题，于是把mypy的版本升级命令直接写在文件里了，这下就没问题了。