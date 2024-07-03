---

类型: 笔记
创建日期: 2024-06-17
修改日期: 2024-06-17
---
## 实验目的
现阶段汽车软件设计大部分遵循了 SOA（面向服务的架构）的指导思想，ROS（Robot Operating System，机器人操作系统）通过服务（service）体现了 SOA 的思想并规范了汽车软件各模块数据格式，广泛用于自动驾驶领域。
本实验旨在通过编写 ROS 应用程序对 ROS 与汽车软件设计相关知识建立初步认识。
## 实验内容
1. 配置 ROS Melodic 环境
2. 验证基于 DDS 通信的样例程序。
## 实验步骤
### ROS 的下载与配置
下载：`wget http://fishros.com/install -O fishros && sudo ./fishros`
![[Pasted image 20240617233858.png]]
配置：
![[Pasted image 20240617233915.png]]
![[Pasted image 20240617234107.png]]
### ROS验证
开启ROS服务`roscore`
![[Pasted image 20240617234831.png]]
#### 检查基础功能
##### 小海龟运行
三个终端分别输入如下命令
`roscore`开启ROS服务
`rosrun turtlesim turtlesim_node`开启小海龟模拟
`rosrun turtlesim turtle_teleop_key`控制小海龟移动
![[Pasted image 20240617235007.png]]
##### rviz
两个终端
`roscore` 开启ROS服务
`rosrun rviz rviz`打开RVIZ服务
![[Pasted image 20240617235054.png]]
### ROS 基本通信
建立ROS工作空间
```
source /opt/ros/melodic/setup.bash
//创建和编译一个 catkin workspace
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
```
![[Pasted image 20240617235232.png]]

编译服务器端和客户端
```
cd helloworld_publisher
mkdir build
cd build
cmake ..
make -j8
```
![[Pasted image 20240618000054.png]]
服务端
![[Pasted image 20240618000132.png]]
客户端
![[Pasted image 20240618000230.png]]

代码运行
开启三个终端
`roscore` 开启ROS服务
第二、三个终端执行 ./helloworld_publisher 与 ./helloword_subscriber
![[Pasted image 20240618000426.png]]

## 结果分析
以发布者节点做例子进行分析。
它的功能是向一个名为`/chatter`的话题发布字符串消息。
```cpp
#include "ros/ros.h"
#include "std_msgs/String.h"
#include <sstream>

int main(int argc, char **argv) {
    ros::init(argc, argv, "another_publisher");  // 初始化ROS节点，节点名为another_publisher
    ros::NodeHandle n;  // 创建一个节点句柄n，用于与ROS系统交互
    
    // 创建一个发布者，发布到/chatter话题，消息类型为std_msgs::String，队列大小为1000
    ros::Publisher publisher = n.advertise<std_msgs::String>("/chatter", 1000);  
    ros::Rate loop_rate(20);  // 设置循环频率为20Hz

    int count = 0;  // 初始化计数器count
    while (ros::ok()) {  // 持续运行直到ROS关闭
        std_msgs::String msg;  // 创建一个std_msgs::String类型的消息
        msg.data = "hello world from another publisher";  // 设置消息内容

        ROS_INFO("%s", msg.data.c_str());  // 使用ROS_INFO宏打印消息内容到日志
        publisher.publish(msg);  // 发布消息到/chatter话题

        ros::spinOnce();  // 处理所有回调函数
        loop_rate.sleep();  // 按照设置的频率休眠
        ++count;  // 计数器增加
    }

    return 0;
}


```
### DDS 原理及理解

DDS（Data Distribution Service）是一种中间件协议和API标准，用于实现发布/订阅模型的数据分发。其主要特点如下：
1. **发布/订阅模型**：DDS采用发布/订阅模型，允许数据发布者和订阅者通过主题进行通信，彼此不需要知道对方的存在。
2. **QoS（服务质量）控制**：DDS支持多种QoS策略，如可靠性、延迟、数据持久性等，以满足不同应用的需求。
3. **分布式系统**：DDS设计用于支持大规模的分布式系统，具备良好的扩展性和容错性。
4. **自动发现**：DDS支持自动发现机制，使得新加入的节点能够自动发现并连接到现有系统中。
5. **多语言支持**：DDS标准支持多种编程语言，包括C++、Java、Python等。
#### 自己的理解
DDS通过发布/订阅模型实现了松耦合的通信方式，这种方式使得系统中的各个组件能够独立开发、部署和运行，从而提高了系统的灵活性和可扩展性。通过QoS策略，DDS能够在网络环境和系统负载变化时，提供稳定可靠的数据传输。此外，DDS的自动发现功能减少了人工配置的复杂性，使得系统更加易于维护和扩展。