---

类型: 笔记
创建日期: 2024-05-25
修改日期: 2024-05-25
---
### `extern`

`extern` 关键字用于声明一个变量或函数是外部的，也就是说它的定义是在别的文件中。这使得变量或函数可以在多个文件中共享。
#### 用途：
1. **外部变量声明**：告诉编译器该变量在另一个文件中定义。
2. **函数声明**：默认情况下，所有函数声明都是 `extern` 的，即使不显式指定 `extern`。
#### 示例：
```c
// file1.c
int globalVar = 10; // 定义一个全局变量

// file2.c
extern int globalVar; // 声明一个外部变量
void someFunction() {
    printf("%d\n", globalVar); // 使用外部变量
}

```

### `static`

`static` 关键字用于控制变量和函数的作用域和生命周期。它可以用于以下几种情况：
1. **静态局部变量**：在函数内部声明的 `static` 变量在函数的多次调用之间保持其值。
2. **静态全局变量**：在文件内部声明的 `static` 变量或函数只在该文件内可见，不会被其他文件访问。
#### 用途：
1. **静态局部变量**：在函数内声明，生命周期为整个程序运行期间，但作用域仅限于函数内部。
2. **静态全局变量**：在文件内声明，作用域仅限于该文件，无法在其他文件中访问。
#### 示例：
```c
// file1.c
static int staticVar = 10; // 文件内可见的静态变量

void func() {
    static int count = 0; // 函数内的静态变量
    count++;
    printf("Count is %d\n", count);
}

// file2.c
extern int staticVar; // 错误：不能访问 file1.c 中的 static 变量
void anotherFunction() {
    // 不能使用 staticVar，因为它在 file1.c 中是静态的
}

```