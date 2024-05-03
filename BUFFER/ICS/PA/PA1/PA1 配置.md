---

类型: 笔记
创建日期: 2024-05-04
修改日期: 2024-05-04
---
##  检查画面, 按键和声音
```
cd am-kernels/tests/am-tests
make ARCH=native mainargs=k run
```
执行报错：
```
joseph@LAPTOP-E1IU5B7P:~/ics2023/am-kernels/tests/am-tests$ make ARCH=native mainargs=k run
# Building amtest-run [native]
+ CC src/tests/rtc.c
...
...
+ CC src/native/ioe/input.c
/home/joseph/ics2023/abstract-machine/am/src/native/ioe/input.c:2:10: fatal error: SDL2/SDL.h: No such file or directory
    2 | #include <SDL2/SDL.h>
      |          ^~~~~~~~~~~~
compilation terminated.
make[1]: *** [/home/joseph/ics2023/abstract-machine/Makefile:110: /home/joseph/ics2023/abstract-machine/am/build/native/src/native/ioe/input.o] Error 1
make: *** [/home/joseph/ics2023/abstract-machine/Makefile:129: am] Error 2
```
发现SDL2未安装成功
```
joseph@LAPTOP-E1IU5B7P:~/ics2023/am-kernels/tests/am-tests$ sudo apt install libsdl2-dev
[sudo] password for joseph:
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 libdbus-1-dev : Depends: libdbus-1-3 (= 1.12.20-2ubuntu4) but 1.12.20-2ubuntu4.1 is to be installed
 libegl-mesa0 : Depends: libglapi-mesa (= 22.0.1-1ubuntu2) but 23.2.1-1ubuntu3.1~22.04.2 is to be installed
 libglib2.0-dev : Depends: libglib2.0-0 (= 2.72.1-1) but 2.72.4-0ubuntu1 is to be installed
                  Depends: libglib2.0-bin (= 2.72.1-1) but 2.72.4-0ubuntu1 is to be installed
                  Depends: libglib2.0-dev-bin (= 2.72.1-1) but it is not going to be installed
                  Depends: libmount-dev (>= 2.35.2-7~) but it is not going to be installed
                  Depends: libpcre3-dev (>= 1:8.31) but it is not going to be installed
                  Depends: libselinux1-dev but it is not going to be installed
 libudev-dev : Depends: libudev1 (= 249.11-0ubuntu3) but 249.11-0ubuntu3.6 is to be installed
 libwayland-dev : Depends: libwayland-client0 (= 1.20.0-1) but 1.20.0-1ubuntu0.1 is to be installed
                  Depends: libwayland-cursor0 (= 1.20.0-1) but 1.20.0-1ubuntu0.1 is to be installed
                  Depends: libwayland-egl1 (= 1.20.0-1) but 1.20.0-1ubuntu0.1 is to be installed
 libx11-dev : Depends: libx11-6 (= 2:1.7.5-1) but 2:1.7.5-1ubuntu0.2 is to be installed
E: Unable to correct problems, you have held broken packages.
```
### SDL2安装
`sudo apt-get update/grade` 无效
先用`sudo apt-get autoclean/autoremove`清理了安装完成后不用的包啥的，不清楚后果，可能是个伏笔

再按照报错一步步下载需要的版本
`sudo apt-get install libglapi-mesa = 22.0.1-1ubuntu2`
...

这下SDL2安装成功了
`sudo apt install libsdl2-dev`
运行测试程序时，按下方向键确实会有相应的信息打印在shell中。

