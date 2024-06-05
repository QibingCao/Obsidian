---

类型: 笔记
创建日期: 2024-06-04
修改日期: 2024-06-04
---
gcc

这段代码是一系列用于创建一个包含`i686-elf-gcc`和`i686-elf-ld`符号链接的目录结构，并将其压缩为`i686-elf-tools-linux.zip`文件的命令序列。让我逐步解释每个命令的作用：

1. `mkdir -p /home/joseph/as/release/download/i686-elf-tools-linux/bin`：
    
    - 创建一个目录结构，如果目录已经存在则不报错（`-p`选项）。
    - 在这里，您创建了一个名为`i686-elf-tools-linux`的目录，其中包含一个`bin`子目录用于存放工具。
2. `cd /home/joseph/as/release/download/i686-elf-tools-linux/bin`：
    
    - 切换当前工作目录至`/home/joseph/as/release/download/i686-elf-tools-linux/bin`。
    - 这一步是为了在`bin`目录下执行后续的创建符号链接的命令。
3. `ln -fs /usr/bin/gcc i686-elf-gcc`：
    
    - 创建一个指向`/usr/bin/gcc`的符号链接，并命名为`i686-elf-gcc`。
    - 这将使`i686-elf-gcc`指向系统中的`gcc`编译器。
4. `ln -fs /usr/bin/ld i686-elf-ld`：
    
    - 创建一个指向`/usr/bin/ld`的符号链接，并命名为`i686-elf-ld`。
    - 这将使`i686-elf-ld`指向系统中的链接器。
5. `echo native > ../../i686-elf-tools-linux.zip`：
    
    - 创建一个名为`i686-elf-tools-linux.zip`的文件，并将字符串`native`写入该文件。
    - 这里使用`echo`命令将字符串写入文件，通常不会将字符串写入压缩文件，这可能只是一个示例。
6. `cd -`：
    
    - 返回到之前的工作目录，即上一个目录。
    - 这一步将您带回到之前的工作目录，以便您可以继续在那里进行其他操作。

总体来说，这段代码的目的是创建一些符号链接，将它们放入一个特定的目录结构中，并最终将该目录结构压缩为一个zip文件。

![[Pasted image 20240605004703.png]]![[Pasted image 20240605190940.png]]
![[Pasted image 20240605190959.png]]