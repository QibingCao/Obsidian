## 问题:
在用spring管理Bean时，可以指定scope为singleton或者prototype。singleton为单例模式，自始至终只会有一个相同class的对象，而prototype在被调用时会new出一个新的对象。
BeanA类和BeanB类循环依赖：BeanA类有一个BeanB的实例，同时BeanB类中也有一个BeanA类的实例。在用spring管理bean时，类中的属性注入时会有循环依赖的风险。
### 解决方法

1. 当Spring容器读取配置元数据（例如，基于XML的配置文件、基于Java的配置类或通过扫描包来查找标注了@Component、@Service等注解的类）并构建应用上下文（ApplicationContext）时，它会创建并初始化标记为单例作用域（singleton scope）的Bean。

可以说singleton的bean是在spring初始化时就被创建的。

2. 利用三级缓存解决循环依赖时的注入，保存bean的半成品。
### 实验
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1710295248957-fe03505a-22ab-4961-8b63-45ffcf11f7f3.png#averageHue=%232e2d2c&clientId=ue38471be-f545-4&from=paste&height=155&id=ua2e3da08&originHeight=256&originWidth=828&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=39134&status=done&style=none&taskId=ue94145af-f074-4cca-9db4-e0affb26d85&title=&width=501.8181528138737)
BeanB有@Lazy注解。

此时创建一个BeanA的实例，结果如下：
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1710331314761-047396ae-a5ed-4e0e-959b-cb0b030ef8ed.png#averageHue=%232c2b2b&clientId=u19ad1aea-369b-4&from=paste&height=226&id=uff950c4e&originHeight=373&originWidth=1555&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=52218&status=done&style=none&taskId=ud422064e-db50-4c55-a321-8dbe02ed50b&title=&width=942.4241879535913)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2699722/1710331327847-52395d0d-1f0c-46a5-962e-66ac8a135dc0.png#averageHue=%23363533&clientId=u19ad1aea-369b-4&from=paste&height=127&id=u7bba3f99&originHeight=209&originWidth=414&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=33071&status=done&style=none&taskId=uab124fcb-8e09-4529-bc84-a719d5fbc43&title=&width=250.90907640693686)
可以看出仍然先创建了singleton的BeanB实例。
