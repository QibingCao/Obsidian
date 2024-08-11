1. 三种继承方式对于基类成员的访问权限是怎样的？

![image-20240319085221214](https://bray07.oss-cn-beijing.aliyuncs.com/undefined202403190852316.png)





2. 多基派生会产生的问题有哪些？怎样解决？

（1）成员名访问冲突的二义性，可以加类作用域访问，但是不推荐 —— 应该尽量避免。

同时，如果D类中定义了同名的成员，可以对基类的这些成员造成隐藏效果，那么就可以直接通过成员名进行访问。

<img src="https://bray07.oss-cn-beijing.aliyuncs.com/undefined202403190855470.png" alt="image-20240319085507433" style="zoom:67%;" />



（2）存储二义性（菱形继承引发的问题），让中间层基类虚拟继承顶层基类解决。

<img src="https://bray07.oss-cn-beijing.aliyuncs.com/undefined202403190858411.png" alt="image-20240319085818332" style="zoom: 67%;" />





3. 编写一个圆类Circle，该类拥有：

   ① 1个成员变量，存放圆的半径；

    ② 两个构造方法 Circle( ) // 将半径设为0 Circle(double r ) //创建Circle对象时将半径初始化为r 

   ③ 三个成员方法 double getArea( ) //获取圆的面积 double getPerimeter( ) //获取圆的周长 void show( ) //将圆的半径、周长、面积输出到屏幕



4. 编写一个圆柱体类Cylinder，它继承于上面的Circle类，还拥有：

   ① 1个成员变量，圆柱体的高；
   ② 构造方法
   Cylinder (double r, double h) //创建Circle对象时将半径初始化为r
   ③ 成员方法
   double getVolume( ) //获取圆柱体的体积
   void showVolume( ) //将圆柱体的体积输出到屏幕 

   编写应用程序，创建类的对象，分别设置圆的半径、圆柱体的高，计算并分别显示圆半径、圆面积、圆周长，圆柱体的体积。

<span style=color:red;background:yellow>**见day13/test/Cylinder.cc**</span>











5. 构建一个类Person，包含字符串成员name（姓名），整型数据成员age（年龄），成员函数 display()用来输出name和age。构造函数包含两个参数，用来对name和age初始化。构建一个类Employee由Person派生，包含department（部门），实型数据成员salary（工资）,成员函数display（）用来输出职工姓名、年龄、部门、工资，其他成员根据需要自己设定。主函数中定义3个Employee类对象，内容自己设定，将其姓名、年龄、部门、工资输出，并计算他们的平均工资。

<span style=color:red;background:yellow>**见day13/test/Employee.cc**</span>





