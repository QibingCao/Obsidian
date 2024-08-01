---

类型: 笔记
创建日期: 2024-07-30
修改日期: 2024-07-30
---

环境配置不再赘述，我是直接git clone的作业文件，在pycharm中使用。因为colab经常性断联还没有历史记录。

## def parse_mnist(image_filename, label_filename)
作业中说明了，为了效率尽量使用numpy而不是python原生的数组➕循环操作
此处要求输出为Tuple(X, y)

先上网站上找到数据的存储定义
https://yann.lecun.com/exdb/mnist/

查到从自定义编码的二进制文件中读取的api是fromfile
https://numpy.org/doc/1.24/reference/generated/numpy.fromfile.html

