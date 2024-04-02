## 编译运行
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709197017207-5c50135e-7f2d-4526-a683-c370c8484388.png#averageHue=%23f6f6f6&clientId=u39b821ca-37ba-4&from=paste&height=286&id=u60f7cb73&originHeight=638&originWidth=966&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=120428&status=done&style=none&taskId=ue7c93a94-e61d-4da1-bbd5-abfe80a2b20&title=&width=433)
PATH环境变量会作为默认路径，先去当前路径，再去默认路径。
javac hello.java 编译出hello.class文件
java hello 运行程序，java后面跟的是【类名】，故无法绝对路径运行，而是要设置环境变量的CLASSPATH到指定的路径，classloader找类名.class文件开始（环境变量一旦配置路径则不会去当前路径找，可以设置一个为 . ，代表当前路径）。

javadoc识别如下注释
/**
* @author name
*/

public class 和 class区别：一个源文件中可有多个class，编译后生成多个.class文件。public class必须和.java文件名一致， public 类可以没有。
每个类中都可以写main入口方法，执行时 【java 类名】。

java保留字goto，const。现在没用但不能做标识符。

java遵循就近原则，变量解析先内后外。

局部变量在方法体中，实例变量在类中，静态变量属于类而不是实例。局部变量必须手动赋值，成员变量（实例变量、静态变量）默认赋值。引用数据类型默认值为null。

## 数据类型
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709199643083-3fd04997-be0f-47cc-a482-48e027d752be.png#averageHue=%23f9f8f7&clientId=u39b821ca-37ba-4&from=paste&height=265&id=u32f0492c&originHeight=602&originWidth=1199&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=219061&status=done&style=none&taskId=ucf3e15b3-239e-434d-845f-28f17883093&title=&width=528.6666870117188)
char占两个字节（Unicode），可以存放一个汉字。char 和short表示的数量是一样的。
多种数据类型混合运算时先转换成最大的再计算，小于int的用int， 'a' -> 97。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709205369828-fc2ec271-07bb-4c5a-b0a7-9eaff68140c8.png#averageHue=%23f8f6f6&clientId=ucb378cb4-070d-4&from=paste&height=247&id=u614ff5bd&originHeight=371&originWidth=921&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=180780&status=done&style=none&taskId=u62749325-b58a-4b7f-a48c-9e31fe3bafa&title=&width=614)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709205452281-1167047f-2933-40ec-af07-51ab5d61e75e.png#averageHue=%23f8f7f7&clientId=ucb378cb4-070d-4&from=paste&height=329&id=udbdbca1a&originHeight=493&originWidth=952&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=270451&status=done&style=none&taskId=u8280c48a-fc3c-431d-9db1-b2cb3c017d8&title=&width=634.6666666666666)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709205487837-a1581b35-8fcf-43a1-9510-b9ff8e0cae27.png#averageHue=%23f9f9f8&clientId=ucb378cb4-070d-4&from=paste&height=239&id=ub28215ed&originHeight=358&originWidth=891&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=158765&status=done&style=none&taskId=u52a80b83-baa6-4a1d-90d9-511e50b56b5&title=&width=594)
方法执行时，内存分配 局部变量表 和 操作数栈。
局部变量表是方法执行过程中用于存储方法参数和方法内部定义的局部变量的一个数组结构。这个表的大小在编译期间就已经确定，并写入到方法的二进制表示中。每个方法调用时，其参数（如果是实例方法，则包括**this**引用）首先会被传递到局部变量表中对应的槽位上。
JVM的指令集是基于栈的指令集，大多数指令都会从操作数栈中弹出操作数，执行操作，然后将结果压回操作数栈。
当一个方法被调用时，JVM首先为该方法创建一个新的栈帧，这个栈帧包含了局部变量表和操作数栈。局部变量表用于存储方法的参数和局部变量，操作数栈则用于执行方法中的各种操作。通过这种方式，JVM能够处理方法调用的嵌套和递归，每个方法调用都有自己独立的栈帧，互不干扰。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709207263341-2d144f4e-6566-4222-a58f-cf05ad79ec74.png#averageHue=%23f3f3f3&clientId=ucb378cb4-070d-4&from=paste&height=169&id=u50c373e5&originHeight=545&originWidth=1156&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=58099&status=done&style=none&taskId=u5e090a6f-4020-4184-b2ae-96700311ef6&title=&width=358.66668701171875)
![](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709209447052-1c660352-8c69-4dc3-a9e1-35df5b232e3e.png#averageHue=%23f4ede6&clientId=u97e8d461-a274-4&from=paste&height=228&id=u866a36c7&originHeight=630&originWidth=604&originalType=url&ratio=1.5&rotation=0&showTitle=false&status=done&style=none&taskId=u92bc7a92-d08e-4237-8382-1173574147f&title=&width=219.00003051757812)
## 语句
jdk7之后switch支持字符串
## 方法
static 方法 调用者和被调的 在同一个类中可以省略。
 方法不调用不分配内存空间，java8之后字节码指令存储在原空间metaspace中（属于本地内存）。
方法调用的瞬间会在栈内存中分配空间，压栈。方法一旦结束弹栈。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709209595958-a3e8810e-e34b-47f9-b4b4-5324a2faf624.png#averageHue=%23eeeeee&clientId=u97e8d461-a274-4&from=paste&height=188&id=u0722e74d&originHeight=546&originWidth=1307&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=127536&status=done&style=none&taskId=u5f86130d-51a1-4111-a2e8-16fa7078e97&title=&width=449.800048828125)
### 重载 overload
同一个类中方法名称相同，参数列表不同。
方法重载是编译阶段的机制，在编译阶段已经确定调用的方法。（静态多态）
### 递归
参考方法运行底层实现。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709211500859-2f799c45-cd57-4384-86fb-8636e9ee2757.png#averageHue=%23f5f4f3&clientId=u1b12e2d4-43b3-4&from=paste&height=331&id=ufa753b6d&originHeight=620&originWidth=1096&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=207043&status=done&style=none&taskId=u9c9cc569-e769-43d8-9256-cad606d1e18&title=&width=584.5333333333333)
## 面向对象
### JVM内存结构
>= Java8
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709214303376-4eb4bae0-21d2-4c5b-9d30-0f33d25afab3.png#averageHue=%23f1f0f0&clientId=u1b12e2d4-43b3-4&from=paste&height=331&id=u55d9e355&originHeight=620&originWidth=1387&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=519084&status=done&style=none&taskId=ud70770c0-1b51-4acd-8acb-280a3bfc95b&title=&width=739.7333333333333)
### JVM栈
在Java程序中，每个线程都会有自己的JVM栈，这个栈与线程同时创建，它包括多个栈帧（Stack Frame），每当调用一个方法时，就会创建一个栈帧用于存储局部变量表、操作数栈、动态链接信息、方法出口等信息。
<!--SR:!2024-04-03,3,250-->

1. **局部变量表（Local Variable Table）**：
   - 存储方法的参数和方法内部定义的局部变量。
   - 局部变量表的容量以变量槽（Variable Slot）为单位，其中64位长度的数据类型（long和double）会占用两个槽位，其它数据类型占用一个槽位。
   - 局部变量表的大小在编译期间确定，运行时不会改变。
2. **操作数栈（Operand Stack）**：
   - 也称为表达式栈，是一个后进先出（LIFO）栈。
   - 用于存储指令过程中的中间计算结果，以及作为指令执行的操作数（如加法指令需要两个操作数）。
   - 操作数栈的深度在编译期间就已经确定。
3. **动态链接（Dynamic Linking）**：
   - 每个栈帧内部包含一个指向运行时常量池中该栈帧所属方法的引用，以支持方法调用过程中的动态链接。
   - 例如，在方法调用中，动态链接可以用于解析被调用方法的直接引用。
4. **方法出口（Method Exit）**：
   - 当方法执行完成后，需要有一个过程来确保当前方法的执行环境可以被正确销毁，并且控制权可以返回到方法被调用的位置。
   - 方法出口包括异常处理表，用于在方法执行时处理异常情况。
5. **异常处理表（Exception Table）**：
   - 在栈帧中用于处理方法执行过程中抛出的异常。
   - 包含了异常处理器的起始位置、结束位置、处理器代码的位置以及需要捕捉的异常类型。
<!--SR:!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250-->

每个线程的JVM栈是独立的，这意味着一个线程不能访问另一个线程的JVM栈。当线程执行Java方法时，JVM会为这个方法创建一个新的栈帧并压入当前线程的JVM栈中。方法结束时，其对应的栈帧会被弹出栈，并且栈帧内的局部变量表和操作数栈也会随之销毁。
#### 运行时常量池
每个类或接口的常量池表的运行时表示形式，它包含了各种字面量和对类型、字段和方法的符号引用。这些信息是在类加载阶段被加载到JVM中，并被存储在方法区内。运行时常量池是动态的，除了类文件中定义的常量信息，还可以在运行时将新的常量加入到池中。

**this**是当前对象的引用，当前对象的地址。
### 构造方法
对象创建＋初始化。new 的时候创建对象，分配空间；完成之后执行【构造方法体】初始化。
**构造代码块**在每一次new时，执行一次，执行时可以调用this说明对象已经创建好了。可用于共同的属性初始化，而无需在每个构造器中重复相同的代码。
<!--SR:!2024-04-03,3,250!2024-04-03,3,250-->

1. **静态变量初始化和静态代码块**（只在类加载时执行一次）。
2. **成员变量初始化**：成员变量按照在类中声明的顺序初始化。
3. **构造代码块**：在每次创建对象时执行，且在构造器执行之前执行。
4. **构造器（构造方法）**：最后执行，用于完成对象的构建。
<!--SR:!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250-->

**super**关键字可以在子类的构造函数中使用，用来显式调用父类的带参构造函数super(参数)。
### this 关键字
**this**关键字存在于局部变量表的第0个槽位上，代表当前对象的引用。
作为思考：在**main**方法中，**args**参数是该方法的唯一参数。由于**main**方法是静态的，不涉及**this**引用，所以**args**参数实际上占据了局部变量表的第0个槽位。
this(实参)：可在构造方法中调用其他带参构造函数，不会创建新的对象，只能出现在构造方法第一行。
### static 关键字
  jdk8之后，静态变量存储在堆中，类加载时初始化。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709310783619-b0967661-7353-493e-959a-dab356c66459.png#averageHue=%23efeded&clientId=u23903597-560f-4&from=paste&height=370&id=u761d675c&originHeight=611&originWidth=961&originalType=binary&ratio=1.3499999046325684&rotation=0&showTitle=false&size=169638&status=done&style=none&taskId=u2edc2ae0-26ac-44d1-a9a1-f95d887e028&title=&width=582.4242087610297)
指向null.静态方法 不会出现空指针异常。
静态方法中不能使用this关键字（类的普通方法也不行），因为静态方法是属于类的在类加载时就初始化好了。
### 静态代码块
在**类加载（类加载进JVM）**时执行，只执行一次，多个按自上而下顺序执行。
### JVM结构
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709365239114-ee731c2a-b56e-4cea-9b10-8ce3a88e859b.png#averageHue=%23fef9f3&clientId=ue259a89c-b2e7-4&from=paste&height=140&id=uf1dd4c13&originHeight=225&originWidth=468&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=93063&status=done&style=none&taskId=uda3b4e37-19a5-4f5c-a930-c4d44e36f8b&title=&width=292)
对于JVM规范有着不同的实现，例如规范来说静态变量应该存储在方法区，但是HotSpot实现时将其放在堆中。
#### JDK6
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709365861076-1ade916f-a6bd-4709-9152-7384440d5f83.png#averageHue=%23dfe2b7&clientId=ue259a89c-b2e7-4&from=paste&height=291&id=u56dc19a5&originHeight=648&originWidth=696&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=144040&status=done&style=none&taskId=u1db0fb16-72c7-4ff6-9a23-fd2c32b8b4f&title=&width=313)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709365999417-192313e5-4297-4a14-9a85-4be401b966a5.png#averageHue=%23f2f2f0&clientId=ue259a89c-b2e7-4&from=paste&height=172&id=uaa30b690&originHeight=335&originWidth=811&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=188013&status=done&style=none&taskId=ub0b5bfe5-0d2b-460f-a350-88e662a1e90&title=&width=415.66668701171875)
#### JDK7 过渡版本
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709366161373-70540f82-5a18-4067-8ba3-36042590f985.png#averageHue=%23b7cda7&clientId=ue259a89c-b2e7-4&from=paste&height=205&id=ufe6ce808&originHeight=375&originWidth=576&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=84400&status=done&style=none&taskId=u53d0a930-56b5-4675-b124-0f030c810f5&title=&width=315)
静态变量和字符串常量池从永久代放入堆，新开辟本地内存存放常量池中的符号引用。
#### JDK 8+
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709366386232-f97b69fe-cb80-49bd-a779-89cf8bcd0b43.png#averageHue=%23aec79c&clientId=ue259a89c-b2e7-4&from=paste&height=173&id=u6599b5bc&originHeight=371&originWidth=668&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=89708&status=done&style=none&taskId=u36c2a362-d7f1-462c-bf71-5013cf658a2&title=&width=312.3333435058594)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709366683008-62ae12ea-8207-45c1-92ff-571c6e2effc1.png#averageHue=%23e5deb2&clientId=ue259a89c-b2e7-4&from=paste&height=269&id=ua2bb6199&originHeight=530&originWidth=579&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=248178&status=done&style=none&taskId=u17725c38-72ff-436e-a608-d10e66c1862&title=&width=294)
### 单例模式
#### 饿汉式
在类加载时就new一个对象，即使不使用
<!--SR:!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250!2024-04-03,3,250-->

1. 构造方法私有化
2. 对外提供给一个静态访问接口
3. 定义静态变量，在类加载时初始化，仅一次
```java
public class Singleton{
    private static Singleton s = new Singleton();
    private Singleton();

    public static singleton get(){
        return s;
    }
}
```
#### 懒汉式
使用对象时才会创建。

1. 构造方法私有化
2. 对外提供静态方法，判断不为null时创建对象
3. 提供静态变量，初始值为null
### 继承
子类 extends 父类{}
java不支持多继承，可以用多层继承间接实现多个类的继承。
构造方法和private不支持继承
### 方法覆盖/重写 override/overwrite
子类重新覆盖父类的方法
具有相同的方法名，形参列表，返回值（可以为子类）
@Override 注解可表明方法为重写父类，在编译阶段提醒编译器。 JDK5
访问权限可以变高，不能变低；抛出异常可以变少，不能变多
方法覆盖针对实例方法而不是静态方法，覆盖和实例变量也没关系，变量不会被覆盖。
#### 向上转型 upcasting
子->父 类似自动类型转换
父类 a = new 子类（）；
实现动态的多态
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709370322790-a5cd3f90-7ef1-4789-99e3-26f8dd5620f4.png#averageHue=%231f2224&clientId=ue259a89c-b2e7-4&from=paste&height=286&id=u18d4ffad&originHeight=643&originWidth=656&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=261324&status=done&style=none&taskId=ua9567321-37c6-4a6c-add2-71f70860a9a&title=&width=291.86669921875)
#### 向下转型 downcatsing
父 ->子 强制类型转换
为避免出现ClassCastException，一般建议用instanceof判断
if(a instanceof 类)
#### 抽象类
抽象类不一定有抽象方法，但有抽象方法的类必是抽象类。包含抽象方法，不对方法进行实现。抽象类虽有构造方法，但是不能实例化；构造方法是给子类用的。子类继承抽象类而不实现抽象方法会报错。
#### super
super只是代号，代表当前对象父类型的特征（父类可以多重继承有很多个），不能单独输出（this是当前的引用，可以单独输出）。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709374065858-c7f3135e-9843-4ce2-92fc-fe93e64a99e8.png#averageHue=%23f4f2f2&clientId=u90d127ea-9fbe-4&from=paste&height=105&id=u1751131d&originHeight=728&originWidth=1742&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=314182&status=done&style=none&taskId=ud2a9928c-485b-4f5e-b413-8f3661b1ecc&title=&width=250.73333740234375)

### final 关键字
final修饰的类不能被继承
final修饰的方法无法被覆盖
final修饰的变量一单赋值无法重新赋值
final修饰的实例变量不能采用系统默认值，必须在构造方法完成前手动赋值。
final修饰一般和static联合使用 -> 常量，属于类不会占过大空间且不会改变。
### 接口 interface
接口是完全抽象的，只有常量和抽象方法，没有构造方法也无法实例化。
一个非抽象的类实现接口，必须把接口内的方法全部实现，不然会报错。
java8之后出现默认方法（为解决接口增加方法导致实现类全部失效）和静态方法（工具类）。
JDK9之后允许定义私有的实例方法（给没人方法用）和私有静态方法（给静态方法用）
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709383552026-0c31453b-a1ec-4d54-9176-3a170a7e03ab.png#averageHue=%23eeeadf&clientId=ub147a31b-c17a-4&from=paste&height=100&id=u7f8e710b&originHeight=187&originWidth=1250&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=236390&status=done&style=none&taskId=u2a84074f-d18a-4e02-ae94-2e08f778b37&title=&width=666.6666666666666)
### 访问控制
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709383786611-7f91c175-1a0e-4dd6-993a-ae3825262880.png#averageHue=%23fbfbfa&clientId=ub147a31b-c17a-4&from=paste&height=227&id=ue26f5628&originHeight=425&originWidth=848&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=125168&status=done&style=none&taskId=ub6bc700b-8d82-49fb-8fbb-16b6567fdc8&title=&width=452.26666666666665)
### object
object.toString()：输出完整类名@hash码
printLn()自动调引用的toString方法
### clone
使用clone方法必须实现clonable接口并重写clone()方法，并且设置成public。
因为clone()方法是protected，所以必须在子类内（指class大括号内）才能调用
## 数组
数组作为对象（隐式继承Object）存储在堆里。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709436357512-e55acc89-a82e-4825-9d62-169da7b07d60.png#averageHue=%23efefef&clientId=uce40b13e-b6c9-4&from=paste&height=242&id=ue61d9eb7&originHeight=667&originWidth=1111&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=127047&status=done&style=none&taskId=u09c17fa9-a5e0-4b68-86fd-d96e3fc17c2&title=&width=403.73333740234375)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709436659885-7ceb3cc4-d168-4dd9-8c29-0afe28a6a975.png#averageHue=%23fefefb&clientId=uce40b13e-b6c9-4&from=paste&height=135&id=u1356527b&originHeight=314&originWidth=868&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=204960&status=done&style=none&taskId=u0891b846-1a0f-4041-b6ae-bdec7c619f3&title=&width=371.933349609375)
### 方法可变长度参数
只能放在参数列表末尾，int ... nums，可当作数组处理。
### 数组扩容
创建一个更大的数组，再使用System.arraycopy()拷进去

## 单元测试

## 算法
Arrays中的api
自己构建类排序，类要实现comparable 接口，重写compareTo接口（返回一个int，表示大小）
Arrays.copyOf(array, length) 
Arrays.copyOfRange(array, start, end+1)
Arrays.asList(1, 2, 3)
## 异常
程序运行时出现的意外，错误，不正常情况
OOP，异常是以类和对象的形式存在的
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709450845832-f38e4efc-6fa4-4d75-8c7c-76c3e1e7e439.png#averageHue=%23f3f2f1&clientId=u10927b63-b80f-4&from=paste&height=219&id=u5df75e22&originHeight=628&originWidth=1244&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=562717&status=done&style=none&taskId=ub014a745-864a-4089-9f30-107763768ee&title=&width=434.73333740234375)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709451823307-1eccf2da-58d2-488f-ba0e-9a6affb8b939.png#averageHue=%23efeeee&clientId=u10927b63-b80f-4&from=paste&height=204&id=ua6c3fa7e&originHeight=680&originWidth=1318&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=245175&status=done&style=none&taskId=u635689b1-80b5-4631-ade4-79164f71871&title=&width=395.73333740234375)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709452048090-77bb5f4f-3c51-4c9f-ab2a-32c3accd323b.png#averageHue=%23212427&clientId=u10927b63-b80f-4&from=paste&height=267&id=u0ca6f353&originHeight=500&originWidth=796&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=256121&status=done&style=none&taskId=ue7646885-a53f-463f-ae15-18a3c7fa504&title=&width=424.53333333333336)
异常处理2种方式，try catch在本类中处理，或者在类名后面加throw XXXexception向调用者抛出。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709453832878-1e076cdb-6980-4019-9dad-5b02dd0d4a0b.png#averageHue=%231e2023&clientId=u10927b63-b80f-4&from=paste&height=320&id=ub0d0ad2e&originHeight=600&originWidth=862&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=253670&status=done&style=none&taskId=u8502af4d-6916-43a5-a38c-b08a46735fb&title=&width=459.73333333333335)
try{
}catch(){
}
finally{ // 一定执行
}

子类重写父类的方法之后，抛出的异常可以更少，不能更多。在使用父类引用的情况下，调用者只能处理父类方法实现中所显示的异常。这意味着子类的行为会超出父类的范围，违反了多态性的原则。
## String
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709470090645-16628f96-c01b-4174-a4a8-e1533f9b4c61.png#averageHue=%231e2123&clientId=u10927b63-b80f-4&from=paste&height=218&id=ub127e3f9&originHeight=409&originWidth=1053&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=308679&status=done&style=none&taskId=u75714f19-f8d9-4505-8faf-b53f3b681cd&title=&width=561.6)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709470771608-9cae68da-74d1-4297-9569-812c2f4d0b56.png#averageHue=%231f2124&clientId=u10927b63-b80f-4&from=paste&height=230&id=u8a1cbea6&originHeight=505&originWidth=1210&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=271071&status=done&style=none&taskId=u1fae3528-cac8-4718-86d7-d6805b23dfc&title=&width=550.7333374023438)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709472325370-72a07aa2-5f00-4a06-9689-10f48232eda7.png#averageHue=%23f2f2f1&clientId=u10927b63-b80f-4&from=paste&height=83&id=uc4dc5238&originHeight=186&originWidth=1195&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=152934&status=done&style=none&taskId=u99daf359-16d0-43a8-84a5-ddd0665f4ee&title=&width=535.7333374023438)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709472765632-0c507ed2-be1b-4b4a-aa8b-b43c8321a34a.png#averageHue=%231f2123&clientId=u10927b63-b80f-4&from=paste&height=210&id=u1445a3b5&originHeight=393&originWidth=1405&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=226023&status=done&style=none&taskId=u4a7cda7f-964b-4daa-a527-6a681862eda&title=&width=749.3333333333334)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709474215523-59a652a0-1e2f-45b4-8322-0a39d2bfc76d.png#averageHue=%23202225&clientId=u10927b63-b80f-4&from=paste&height=182&id=u55ff7521&originHeight=341&originWidth=573&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=115171&status=done&style=none&taskId=u13233c74-66b8-4156-a934-c1a5154f54f&title=&width=305.6)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709474341142-8895df40-eb41-4863-8914-e5ed5bd34482.png#averageHue=%23202225&clientId=u10927b63-b80f-4&from=paste&height=159&id=u6d909d47&originHeight=298&originWidth=550&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=95594&status=done&style=none&taskId=ua0936218-1582-4ef6-8a8e-264d34b4753&title=&width=293.3333333333333)
+ 运算符用于连接字符串。根据Java的规则，当 + 运算符的操作数是字符串和 null 时，Java会进行特殊处理。这里的 s1 是 null，而 null 不是一个对象，所以不能直接用于字符串连接。在内部，Java会转换 null 为字符串 "null"，这一转换过程可以认为是通过调用 String.valueOf(null) 完成的，该方法会检查 null 并返回字符串 "null"。因此，实际发生的连接过程相当于 "null" + "null"。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709474689827-7087eca5-ab64-4e7e-a65e-9b9ca518e406.png#averageHue=%231f2224&clientId=u10927b63-b80f-4&from=paste&height=220&id=u5ab351b3&originHeight=412&originWidth=400&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=85140&status=done&style=none&taskId=ud2fb1c59-5f1e-4a0e-b735-106eb8cf465&title=&width=213.33333333333334)
字面量相连接在编译阶段就生成链接后的结果，并保存在常量池中。
### 面试题
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709475037434-b09648fd-bae4-45eb-9981-9e9003b6f60e.png#averageHue=%231e2123&clientId=u10927b63-b80f-4&from=paste&height=223&id=u93676558&originHeight=419&originWidth=482&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=76839&status=done&style=none&taskId=u9aed6d62-440d-43e6-842e-cf426cb484f&title=&width=257.06666666666666)
在Java中，如果try块执行了一个return语句，那么该值会被暂时保存起来。然后finally块会执行。如果finally块中也有一个return语句，它会覆盖掉try块中的return值。因此，无论try块中的代码是否尝试返回某个值，只要finally块中有一个return语句，它都会被执行并且其值将是方法的最终返回值。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709475392770-a36c572e-cc6e-4716-9383-81ddc4470978.png#averageHue=%231e2023&clientId=u10927b63-b80f-4&from=paste&height=255&id=u1ae22dec&originHeight=478&originWidth=430&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=85834&status=done&style=none&taskId=ucedc856b-c943-4ab2-84a1-d83d9b4e9b2&title=&width=229.33333333333334)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709475809299-ce1b6e7a-f94a-44e3-9bab-30f4d0d250dd.png#averageHue=%231e2123&clientId=u10927b63-b80f-4&from=paste&height=454&id=u64a3c600&originHeight=851&originWidth=523&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=196373&status=done&style=none&taskId=u403e53ec-93df-4915-96e1-5ad4bc148a2&title=&width=278.93333333333334)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709475955802-54b64931-cfd9-499c-af56-bb15e47433ad.png#averageHue=%23212528&clientId=u10927b63-b80f-4&from=paste&height=172&id=u40196435&originHeight=323&originWidth=208&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=105797&status=done&style=none&taskId=ubfa29a7c-64ce-4446-9b6c-ea4bb1bc28e&title=&width=110.93333333333334)
静态代码块执行前的【构造...执行】，其实属于static a = new B()静态代码块执行的一部分。
### StringBuilder StringBuffer
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709476211147-b284414d-94c8-4527-a3a0-c883159e3c67.png#averageHue=%23f1f0ee&clientId=u10927b63-b80f-4&from=paste&height=221&id=u0d3219d1&originHeight=415&originWidth=1210&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=374054&status=done&style=none&taskId=uae47ccd7-4a37-49af-85dc-bdaa68c41d3&title=&width=645.3333333333334)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709478286963-26b081d7-9635-44dd-ad0f-0d70998974bb.png#averageHue=%23f5f4f4&clientId=u10927b63-b80f-4&from=paste&height=193&id=ub6584d01&originHeight=362&originWidth=841&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=144212&status=done&style=none&taskId=ub4f18846-d2c1-49fa-b124-de93e42e24e&title=&width=448.53333333333336)
#### 自动装箱 自动拆箱
编译阶段功能。
#### 整数型常量池
-128 -- 127 的Integer [] integerCahce
## 枚举类型
底层实现
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709478845825-75c3e77b-d2f6-4bb7-ad9b-6a7d33307f32.png#averageHue=%23111110&clientId=u10927b63-b80f-4&from=paste&height=87&id=u43d65fa0&originHeight=164&originWidth=936&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=118340&status=done&style=none&taskId=u617f2d21-abe9-47cd-b831-ac870f3a9ce&title=&width=499.2)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709479174261-97f55daf-f958-4505-a56c-a3d2b2ecbae7.png#averageHue=%23f3eceb&clientId=u10927b63-b80f-4&from=paste&height=285&id=u3492b44d&originHeight=534&originWidth=937&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=290844&status=done&style=none&taskId=ud2cf967d-9e70-40ce-bd08-eba9defd57e&title=&width=499.73333333333335)
## 集合
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709480337536-c4519f19-2223-4052-aa97-3a3c922325a9.png#averageHue=%23f2f2f2&clientId=u10927b63-b80f-4&from=paste&height=471&id=u1887584a&originHeight=884&originWidth=1079&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=219908&status=done&style=none&taskId=u8e976eec-66a0-441f-9399-6eea5330c52&title=&width=575.4666666666667)
Collection存放单个数据的集合，
Map存放键值对的集合。

Collection.contains()底层调用的是equals方法，remove()同样在底层拿equals比较元素是否相等。
### iterator
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709533160372-db38b53c-0098-4435-9c4b-ed4ff14e1cc1.png#averageHue=%23f7f6f6&clientId=udeb1ced1-d621-4&from=paste&height=232&id=u4197617e&originHeight=712&originWidth=1681&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=357613&status=done&style=none&taskId=uf9bffbf5-da64-4c05-99f2-3f9d1cc505e&title=&width=548.7333374023438)
## 泛型
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709533920727-10a56ea0-f9be-4014-915c-419d7c518580.png#averageHue=%23f3edec&clientId=udeb1ced1-d621-4&from=paste&height=212&id=u45f70894&originHeight=560&originWidth=1327&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=415295&status=done&style=none&taskId=ua057a27e-d60c-41f4-b3e6-e28b0240e80&title=&width=502.73333740234375)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709534057301-5817e95c-f44c-4613-9b06-7b7d2da2763b.png#averageHue=%23eeedeb&clientId=udeb1ced1-d621-4&from=paste&height=237&id=u439139cb&originHeight=534&originWidth=1220&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=528618&status=done&style=none&taskId=uf51efac2-9cbd-4839-9828-6d4a473cd27&title=&width=540.7333374023438)
#### 类上自定义泛型
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709534624518-08cc6336-2a2c-4fa2-a5fb-0a64f6c77e5e.png#averageHue=%231f2123&clientId=udeb1ced1-d621-4&from=paste&height=181&id=u4bb11494&originHeight=436&originWidth=924&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=131587&status=done&style=none&taskId=u3cc00b00-5c5b-4ec6-9026-fb8ef50b36a&title=&width=383.8000183105469)
#### 静态方法自定义泛型
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709534469506-a0550312-03f3-40aa-9e98-37e3537dd951.png#averageHue=%231f2124&clientId=udeb1ced1-d621-4&from=paste&height=162&id=u057f8df6&originHeight=307&originWidth=575&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=88408&status=done&style=none&taskId=u8f13ec4f-e777-4bc2-a60e-27e27c85d2c&title=&width=302.66668701171875)
#### 接口定义泛型
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709534868786-081a5609-5168-4fd1-9cc4-cd7d4730a118.png#averageHue=%231f2224&clientId=udeb1ced1-d621-4&from=paste&height=113&id=u12cecde1&originHeight=211&originWidth=629&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=63617&status=done&style=none&taskId=u1cc3668d-0a99-4710-b34c-bb29ad6e0d0&title=&width=335.46666666666664)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709534843070-01e4e217-0375-46c5-8235-b2e91ef0fb85.png#averageHue=%231f2124&clientId=udeb1ced1-d621-4&from=paste&height=166&id=uaf7024bd&originHeight=311&originWidth=592&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=76160&status=done&style=none&taskId=u32177d23-b7ec-46df-8e3b-17ea02ac319&title=&width=315.73333333333335)
### 集合迭代过程中的元素删除问题
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709535845417-bfaffbbc-57bd-447e-a000-3c2a9ad52746.png#averageHue=%231f2123&clientId=udeb1ced1-d621-4&from=paste&height=207&id=uc043aac7&originHeight=389&originWidth=928&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=167641&status=done&style=none&taskId=u3270814e-dbec-470b-8840-e1593918f30&title=&width=494.93333333333334)
不要使用集合中的remove方法，使用迭代器中的remove方法。
#### fail-fast方法
发现并发操作就立即抛出并发修改异常，上图中在使用迭代器的情况下调用collection的remove方法也属于这类情况。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709536415878-44a74ce7-f3e9-4591-a7c6-e5d81931ce51.png#averageHue=%23eee4e2&clientId=udeb1ced1-d621-4&from=paste&height=230&id=uc84a353e&originHeight=479&originWidth=1221&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=444424&status=done&style=none&taskId=u4f9ca96a-74c2-4e0c-b887-cb7f2409886&title=&width=586.7333374023438)
### List的comparator
在list元素集合对象的sort方法中传入比较器对象，按自定义比较函数比较。
自己定义比较器类，实现comparator接口，在其中重写compare方法。
也可以使用匿名内部类（lambda表达式）
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709537975038-c6969ad2-94e4-4767-bac8-3eeb2f1a541f.png#averageHue=%231f2225&clientId=udeb1ced1-d621-4&from=paste&height=134&id=u20dd87c2&originHeight=252&originWidth=553&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=75441&status=done&style=none&taskId=u740c50ad-755f-4e89-8f58-2dd6ed64f91&title=&width=294.93333333333334)
### ArrayList源码分析
底层实现为objct数组，故使用场景为大量随机访问，较少增删。
默认初始化为【长度为0的数组】，添加第一个元素后扩容至10。
每次扩大1.5倍。
### LinkedList源码分析

## Set集合
无序不可重复。
hashSet底层实现是hashMap
## Map集合
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709540004202-419fb50e-bb1f-46b6-8ba3-ed99a5d25c2a.png#averageHue=%23f2f2f2&clientId=udeb1ced1-d621-4&from=paste&height=344&id=uac53c608&originHeight=816&originWidth=1127&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=246071&status=done&style=none&taskId=u3ca019dd-174f-444f-abfa-760b311bac4&title=&width=475.06671142578125)
遍历Map集合
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709540404734-9c803b67-3174-467a-aefc-f8ca1b0bca4c.png#averageHue=%231f2124&clientId=udeb1ced1-d621-4&from=paste&height=65&id=ua1d11aed&originHeight=122&originWidth=406&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=32209&status=done&style=none&taskId=uf74e7848-6017-40f8-ae4b-4aa5f26e651&title=&width=216.53333333333333)
推荐使用：
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709540553950-78bb053b-8261-4364-9646-804c9dc137b9.png#averageHue=%23efefee&clientId=udeb1ced1-d621-4&from=paste&height=157&id=ud6f7c10a&originHeight=653&originWidth=1506&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=192114&status=done&style=none&taskId=u14594a7e-3aa1-4c26-a9db-f24bd35c938&title=&width=361.7333679199219)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709540612855-576834da-302a-4d54-a13a-f74a54ff9ec9.png#averageHue=%231f2225&clientId=udeb1ced1-d621-4&from=paste&height=164&id=u5573b5c0&originHeight=308&originWidth=704&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=184307&status=done&style=none&taskId=uba157feb-1f64-41d3-90a4-daf024364ed&title=&width=375.46666666666664)
### hashMap
哈希表项为【k，v，hash，next】hash值为key的hashcode。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709541932808-879b9974-52ff-4bfb-b43f-b2c2a1308ce1.png#averageHue=%23edeceb&clientId=udeb1ced1-d621-4&from=paste&height=283&id=ua498e55e&originHeight=530&originWidth=1275&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=329559&status=done&style=none&taskId=u885ad8b3-4547-4b0c-8a28-6c8fe5f632a&title=&width=680)
要保证无序不可重复，在equals相等时，hashcode必须相等。故通常要将equals方法和hashcode方法全重写。（同时也适用hashSet）

Map可以存null，但只会有一个，固定存在哈希表为0的地方。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709542967923-e4417367-8a90-402e-9991-8fdfbbd46c41.png#averageHue=%23f5f4f4&clientId=udeb1ced1-d621-4&from=paste&height=355&id=u8bf5246c&originHeight=666&originWidth=1206&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=336741&status=done&style=none&taskId=ub55bb7a8-e326-4bd8-85d2-cddacb4f316&title=&width=643.2)
hashMap的初始化容量是2的幂，将取模 hash%length 转化为hash & length -1。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709543204211-240cf2e6-0a2a-40ae-a787-c6d4c11ec123.png#averageHue=%23e6e1d5&clientId=udeb1ced1-d621-4&from=paste&height=346&id=udc359de8&originHeight=649&originWidth=1274&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=534214&status=done&style=none&taskId=u08df8e74-c3e1-42ab-ad0a-48e06ef95c2&title=&width=679.4666666666667)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709543569002-b8a221a1-5570-4a63-9e1f-9fbfb1042755.png#averageHue=%23ede8dc&clientId=udeb1ced1-d621-4&from=paste&height=305&id=u02662190&originHeight=572&originWidth=1255&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=686382&status=done&style=none&taskId=udeffe72f-b2bd-4a04-aa66-5339068ef84&title=&width=669.3333333333334)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709544083849-443f566d-7746-4f42-babf-e26ccac99c1a.png#averageHue=%231e2122&clientId=udeb1ced1-d621-4&from=paste&height=151&id=u423c4d2c&originHeight=284&originWidth=926&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=173608&status=done&style=none&taskId=uac19940a-37de-484b-a452-7e5f79124de&title=&width=493.8666666666667)
#### properties
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709544559296-79fcf650-076f-4bc8-992a-5330646f2d92.png#averageHue=%231e2224&clientId=udeb1ced1-d621-4&from=paste&height=190&id=u622cd028&originHeight=357&originWidth=1038&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=278027&status=done&style=none&taskId=uaa2eeb3a-9fd9-43a1-a989-8ebc22113a3&title=&width=553.6)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709544518372-426b221a-bb1c-4347-875b-60e50d5febfb.png#averageHue=%23f5f4f3&clientId=udeb1ced1-d621-4&from=paste&height=188&id=uda4d42ad&originHeight=352&originWidth=786&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=165538&status=done&style=none&taskId=u56cbb26a-195c-4750-9adc-66aed19a89c&title=&width=419.2)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709544832985-0bf782e5-afba-4ec9-bd11-d0ad52f23ff1.png#averageHue=%231f2224&clientId=udeb1ced1-d621-4&from=paste&height=139&id=u99e0d183&originHeight=260&originWidth=417&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=92230&status=done&style=none&taskId=u3bf753c6-c82e-4ba8-b19b-d1084a65cec&title=&width=222.4)
#### 题目
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709545252432-4f0f1e00-4f63-4964-a2bd-de2cf8751ffe.png#averageHue=%23212427&clientId=udeb1ced1-d621-4&from=paste&height=234&id=u8e5b07bc&originHeight=439&originWidth=520&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=183370&status=done&style=none&taskId=ubabb1e76-b4f6-4e79-afac-c857972f47b&title=&width=277.3333333333333)
### 集合通用工具
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709545343679-a6d615ce-0c88-42dc-ab51-41f079257fbc.png#averageHue=%23f6f5f4&clientId=udeb1ced1-d621-4&from=paste&height=127&id=u3b6dafcf&originHeight=238&originWidth=467&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=63704&status=done&style=none&taskId=ue0e2aaf3-35fb-4297-8947-b0cdb56864a&title=&width=249.06666666666666)
## IO流
java流中以stream结尾的都是字节流，以reader，writer结尾的都是字符流。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709553592545-783c612e-de49-448e-a71a-620d768dbb82.png#averageHue=%23fbf7f2&clientId=udeb1ced1-d621-4&from=paste&height=491&id=u6498d861&originHeight=920&originWidth=1120&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=311120&status=done&style=none&taskId=u9dee9389-6249-497a-92e8-005b3fd4311&title=&width=597.3333333333334)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709555276366-a0efba86-c3a4-4b06-ac9c-b7036b9b2120.png#averageHue=%23f3f2f2&clientId=udeb1ced1-d621-4&from=paste&height=283&id=ua9269a10&originHeight=531&originWidth=1390&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=154195&status=done&style=none&taskId=u49a91fec-f69a-4886-b29a-442d3d49fa2&title=&width=741.3333333333334)
当类实现serializable接口，编译器自动加一个序列化版本号。serialVersionUID
Java中区分类的版本：类名+序列号;
可以通过将序列号写死，即使后期改了类的代码，序列化反序列化时依旧可以当原来的类处理。
transient修饰的不会参与序列化，会保存默认值。
### 装饰器模式
通过装饰器模式实现松耦合下的代码方法改进。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709558774070-13e23cbe-a2e2-4f25-a9a4-38375c7192ef.png#averageHue=%23f2f0f0&clientId=udeb1ced1-d621-4&from=paste&height=264&id=u42c0a824&originHeight=495&originWidth=491&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=60359&status=done&style=none&taskId=u3d32a559-b1c2-4290-b483-02d8de4f139&title=&width=261.8666666666667)
用序列化反序列化实现深克隆。
将对象写入字节数组，读入对象就是深克隆。
（目前三种深克隆方法）
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709559465213-264763c7-eb52-4838-b3e8-74a81cbec316.png#averageHue=%23dcdcd1&clientId=udeb1ced1-d621-4&from=paste&height=328&id=ubfc18b4e&originHeight=615&originWidth=1176&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=368579&status=done&style=none&taskId=u64c46d3b-8bb2-4601-8e33-15fd45c4631&title=&width=627.2)
## 多线程
java代码运行在JVM上，JVM运行程序就是一个进程。在一个进程中有多个线程，JVM中有一个主线程和多个子线程，主线程调用main方法。多个线程共用堆以及方法区，线程都有自己的方法栈，PC，以及本地方法栈。
多线程并发并行皆有，取决于运行时的硬件状态。
java抢占式调度。
### 实现多线程的方法

1. 编写一个类继承Thread，重写run()方法，new线程对象，调用start()方法。
1. ![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709561295044-a0bae348-366c-4570-904e-da821e8178fa.png#averageHue=%231e2123&clientId=udeb1ced1-d621-4&from=paste&height=403&id=vSl4c&originHeight=755&originWidth=605&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=247874&status=done&style=none&taskId=ub215c0db-0b70-4dbe-9509-625769f92fb&title=&width=322.6666666666667)
1. ![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709561586324-c74a025b-176e-44a3-ba8a-ac7eac2b8e3d.png#averageHue=%23f6f3f3&clientId=udeb1ced1-d621-4&from=paste&height=377&id=u30a1b60a&originHeight=707&originWidth=1346&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=194195&status=done&style=none&taskId=u8ef9bc00-f9c9-41bc-b44f-337c2261cb8&title=&width=717.8666666666667)
2. 编写一个类实现runnable接口，实现run()方法，new一个【线程对象】new Thread(new 类)，调用线程的start()方法。

一般推荐实现接口，因为实现接口还保留了类的继承，自由度更大。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709562089368-329ad878-00c2-4f32-85e2-a9489c07ba26.png#averageHue=%231f2124&clientId=udeb1ced1-d621-4&from=paste&height=155&id=u3faf657a&originHeight=291&originWidth=934&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=114509&status=done&style=none&taskId=u8cd73741-9527-4c6d-b796-0ca332b079d&title=&width=498.1333333333333)
#### 线程的生命周期
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709562920749-4f153dde-68db-433a-862a-6af2e32eba9f.png#averageHue=%23ebebeb&clientId=udeb1ced1-d621-4&from=paste&height=333&id=u6bc48438&originHeight=624&originWidth=1244&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=449058&status=done&style=none&taskId=u433a44a7-98a5-464b-be8b-48052c16d21&title=&width=663.4666666666667)
run()方法不能抛出异常。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709563355589-0d24ab7a-83da-4161-8e22-a8c4d6819530.png#averageHue=%231f2124&clientId=udeb1ced1-d621-4&from=paste&height=323&id=u7fbfcf1b&originHeight=606&originWidth=931&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=240497&status=done&style=none&taskId=u99f71ad6-7452-47fd-99ae-1176f2f7759&title=&width=496.53333333333336)
Thread.sleep()是静态方法，让当前线程sleep。
interrupt()为实例方法，构造异常，打断进程sleep。
采用在线程代码中设置标志合理终止线程运行，运行时检查标志状态。
#### 用户线程 守护线程
所有用户线程结束之后，守护线程自动结束。
线程.setDaemon(true)
#### 线程合并
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709564723473-9a505a99-443c-4ed6-ac57-0e244621f88d.png#averageHue=%231e2022&clientId=udeb1ced1-d621-4&from=paste&height=275&id=u67e54037&originHeight=515&originWidth=1062&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=274163&status=done&style=none&taskId=u06ba5cd2-310f-4f65-a158-fc19518b2c1&title=&width=566.4)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709564852543-ece8bd80-cbff-4356-b258-f9e6677c4b8f.png#averageHue=%23202328&clientId=udeb1ced1-d621-4&from=paste&height=138&id=ua1282568&originHeight=259&originWidth=685&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=115281&status=done&style=none&taskId=ue7189c32-7ea4-4d36-b737-14ce8e5cdf5&title=&width=365.3333333333333)
#### JVM线程调度
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709564989634-055d1d08-6763-43bf-9bc0-7e269f0f3dc8.png#averageHue=%231e2122&clientId=udeb1ced1-d621-4&from=paste&height=134&id=u48396e5c&originHeight=251&originWidth=810&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=113142&status=done&style=none&taskId=u50641edb-8463-470c-9b03-8068903f599&title=&width=432)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709565089188-c035740d-e4f2-427f-b36e-3e6d76b1247e.png#averageHue=%231e2022&clientId=udeb1ced1-d621-4&from=paste&height=134&id=u1d28d645&originHeight=252&originWidth=995&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=101448&status=done&style=none&taskId=u7a9162ff-cfa0-4b82-a36b-b142a7f9e61&title=&width=530.6666666666666)
#### 线程安全
并发涉及到修改共享数据，就会涉及到线程安全问题。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709565238078-519fd317-f482-41f7-87b0-2a3b8b54a039.png#averageHue=%231e2022&clientId=udeb1ced1-d621-4&from=paste&height=188&id=ucc09a684&originHeight=352&originWidth=1280&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=182305&status=done&style=none&taskId=u04eea9cd-b461-4ad9-aab4-01e0a463305&title=&width=682.6666666666666)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709565776020-b35a7608-5667-4bef-a240-31275dcdd7a8.png#averageHue=%231e2123&clientId=udeb1ced1-d621-4&from=paste&height=283&id=u8cbec3fc&originHeight=531&originWidth=1421&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=286458&status=done&style=none&taskId=ua7c9002d-04d0-4478-b89f-5fed90d9d5b&title=&width=757.8666666666667)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709565993103-7e40eafe-8ef0-4f6e-876e-b129e2bb1cdd.png#averageHue=%231f2123&clientId=udeb1ced1-d621-4&from=paste&height=226&id=ud8cc2670&originHeight=423&originWidth=801&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=156065&status=done&style=none&taskId=u4727c3ff-b113-411b-a406-d20d13aa2d0&title=&width=427.2)
### 线程通信
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709622146562-18187b6c-13de-45dd-9570-95474decab32.png#averageHue=%23f8f6f6&clientId=u5ab4bb86-3c2b-4&from=paste&height=315&id=u396d8c03&originHeight=590&originWidth=1161&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=443369&status=done&style=none&taskId=u4b1324ad-c337-4715-b6ce-d00837260e4&title=&width=619.2)
wait notify的对象是 需要同步的那个变量
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709622518966-84a95312-9712-4b7e-bda7-9922f5d3c3a8.png#averageHue=%23ededed&clientId=u5ab4bb86-3c2b-4&from=paste&height=386&id=ue2bd5a76&originHeight=723&originWidth=1297&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=459081&status=done&style=none&taskId=u8337b27b-c6c8-466b-9d03-f59c10ad01c&title=&width=691.7333333333333)
#### 线程安全的懒汉式单例模式

1. 直接给singleton的get方法加锁，不允许多个线程同时执行
2. 加类锁，利用反射机制获取singleton的类，给这个类加锁
3. 加判断，只有第一次调用方法时加锁。
4. ![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709623614230-efc2cb76-5b73-4574-b473-827761242d15.png#averageHue=%231f2224&clientId=u5ab4bb86-3c2b-4&from=paste&height=274&id=u9d40728f&originHeight=513&originWidth=610&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=183455&status=done&style=none&taskId=u9bf09376-3514-41c5-b007-19cee99450c&title=&width=325.3333333333333)

还要考虑valotile，指令重排，防止编译器底层搞代码优化。
### reentrant lock 可重入锁
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709623949075-6bf95fa4-beb1-4f18-9c58-3e27cc33f11c.png#averageHue=%231f2123&clientId=u5ab4bb86-3c2b-4&from=paste&height=454&id=u3c202523&originHeight=852&originWidth=1067&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=351808&status=done&style=none&taskId=u0867f671-7739-4178-823a-50bbdebdc22&title=&width=569.0666666666667)

#### 实现线程的第三、四种方式
实现callable接口，实现call方法。建立FutureTask(callable x)对象，传入线程对象。

主线程等待返回值时是阻塞的。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709624460165-03105b4b-a323-45af-8c9e-6842b049ed4b.png#averageHue=%231f2123&clientId=u5ab4bb86-3c2b-4&from=paste&height=482&id=u93a04ae1&originHeight=904&originWidth=906&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=343227&status=done&style=none&taskId=ub4b78a46-6131-41bd-85ae-9e925f36bb5&title=&width=483.2)
创建线程池
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709624708746-4f780a9e-339c-46a7-a038-883267853faf.png#averageHue=%231f2123&clientId=u5ab4bb86-3c2b-4&from=paste&height=450&id=u80254d48&originHeight=844&originWidth=1054&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=383981&status=done&style=none&taskId=u42a67585-66de-4b83-a23d-a3b23257c46&title=&width=562.1333333333333)
## 反射机制 Reflect
### 获取class的方式
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709626529571-df9ffec8-c8ed-4ffc-94a9-56e73eb153fe.png#averageHue=%231e2123&clientId=u5ab4bb86-3c2b-4&from=paste&height=310&id=ub8775b00&originHeight=582&originWidth=983&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=403002&status=done&style=none&taskId=ue797caa9-ba27-4d10-994c-fd54b63a174&title=&width=524.2666666666667)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709626786280-39ecfed7-660d-4692-b813-ada7078feaa5.png#averageHue=%231f2223&clientId=u5ab4bb86-3c2b-4&from=paste&height=61&id=u6580b3e1&originHeight=114&originWidth=1033&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=85242&status=done&style=none&taskId=uf3063147-c97a-4f62-8e6f-d2730ebc1a3&title=&width=550.9333333333333)
#### 通过Class来新建对象
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709627332649-7834403c-a345-4185-b488-8c4663673577.png#averageHue=%231e2123&clientId=u5ab4bb86-3c2b-4&from=paste&height=404&id=u94c0734f&originHeight=757&originWidth=1109&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=329020&status=done&style=none&taskId=u5489890a-dc0b-4004-bf03-2524c4dc204&title=&width=591.4666666666667)
newInstance调用类的无参构造方法，若没有则会报异常，已经过时。
采用constructor来调用构造方法

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709630352632-1e385696-6987-4d9c-b642-a18f0860fcc8.png#averageHue=%23d8d5ce&clientId=u5ab4bb86-3c2b-4&from=paste&height=313&id=u2a454201&originHeight=587&originWidth=1460&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=371314&status=done&style=none&taskId=u72b3bc6c-e8c2-4017-857f-6e13c4dc909&title=&width=778.6666666666666)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709630915377-ca86035c-46c9-4808-97a4-6db624f5ad53.png#averageHue=%231f2124&clientId=u5ab4bb86-3c2b-4&from=paste&height=322&id=u6290608b&originHeight=604&originWidth=1126&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=325138&status=done&style=none&taskId=u344eb416-dd5b-4a11-8a7b-4287e46b14f&title=&width=600.5333333333333)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1709631603696-048e2a83-8192-43d5-a144-47541f70ccb0.png#averageHue=%23f7f7f7&clientId=u5ab4bb86-3c2b-4&from=paste&height=314&id=u1f2e5787&originHeight=589&originWidth=1329&originalType=binary&ratio=1.875&rotation=0&showTitle=false&size=299915&status=done&style=none&taskId=u9a99f1ab-7efb-4f31-abdb-f177767f44b&title=&width=708.8)

## 注解
## 网络编程
## Lambda表达式
