# 移植linux

## 前言

linux就是一个大型c语言程序，移植linux就是让这个c语言程序在我们的cpu中可以跑起来

所以在移植linux前，我们先学习怎么移植小型的c语言程序

- 循序渐进：理解了小型c语言程序的移植，才方便我们移植linux这种大型c语言程序

- 方便调试：linux运行失败可能是cpu组或者外设组的问题，可以先尝试运行一些小型程序，方便查找到底是谁的锅

  

## 移植“Hello World”

先想办法让这个程序在cpu中跑起来

```c
#include <stdio.h>

int main(){
	printf("Hello, World\n");
}
```

### 函数库

首先考虑用什么libc 函数库，它提供`<stdio.h>`等函数库供我们使用

绝大多数的 Linux 发行版使用的 libc 库都是 Glibc，但也可以考虑Musl等其他函数库，因为Glibc并不完美。



下面这张表展示了不同 libc 库编译的文件大小。

![1a2a4241bb85aa0e319f4a00abb547d5](/home/cyh/cpu_env/resource/1a2a4241bb85aa0e319f4a00abb547d5.png)

除此之外还有代码写得很难看懂，对静态链接支持不佳等问题，但Glibc性能好于Musl。



所以在一些项目中，他们会提供选择给我们

![a9ef4b8b7c12d99d3e4a3d7cd7231326](/home/cyh/cpu_env/resource/a9ef4b8b7c12d99d3e4a3d7cd7231326.png)





### 输出

再看`printf`函数

#### 裸机运行时环境

`printf`会调用函数a,函数a又会调用函数b,...,最后会调用`putch`

``` c
#define SERIAL_PORT 0xa0003f8

void putch(char ch) {
  *(volatile uint8_t  *)SERIAL_PORT = data;
}

```



它会变成一个访存指令，cpu发现`0xa0003f8`是串口（uart）所在地址，就会对它进行写值。串口可以把这些值传递给上位机。

`0xa0003f8`这个地址需要提前约定好，根据情况修改，这是linux不能直接运行到我们cpu的原因之一。

#### 操作系统

1. `printf`会经过一堆中间函数后调用系统调用函数（_syscall），然后**中断**程序运行，保存相关信息，把cpu的使用权移交给操作系统。对应到汇编则主要是`ecall`指令。
2. 操作系统会运行系统调用函数，又会经过一堆中间函数后，与显存交互，使字符显示在屏幕上。
3. 最后恢复程序运行。



与显存交互同样需要我们进行适配，也就是写驱动。



### 堆栈

​	**栈区：**适合存放函数调用和局部变量等数据

​	**堆区：**适合动态分配和大内存



main函数里没有？运行main函数之前就把这些工作做完了！

main函数并不是程序的入口，同样return也不是程序的结束。比如程序执行前需要给它分配内存，程序完后要给它释放内存，这些是操作系统的工作之一。



### 交叉编译

我们的电脑一般是x86指令集，但我们写的cpu是riscv指令集。所以需要交叉编译工具链让x86的电脑能编译出riscv的二进制文件。

- **[riscv-gnu-toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain)**提供了我们需要的全套工具链

- 中科院软件所也提供了一份RISC-V工具链镜像[riscv-gnu-toolchain](https://help.mirrors.cernet.edu.cn/riscv-toolchains)



### 各种文件与工具

#### Makefile/*.mk

git clone 一个项目后，经常会先让我们make xxx，这是把各种命令简化成了make xxx

#### *.sh

shell脚本，也是用于执行这种命令行的

#### *.ld

用于链接项目的脚本。链接脚本决定了 elf 程序的内存空间布局(严格的讲是虚存映射，程序中的各种绝对地址就在链接的时候确定)

#### *.S

汇编代码，有些函数不适合c语言来写，可以用汇编写



