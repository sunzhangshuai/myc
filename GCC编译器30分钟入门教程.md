# 概念

- **GNU**：全称 `GNU's Not UNIX` ，又被称为『革奴计划』。
- **GCC**
  - 早期：` GNU C Compiler`。
  - 重新定义：`GNU Compiler Collection`、 GNU 编译器套件。

# 下载与安装

- **查看版本**

  ```shell
  gcc --version
  ```

  > c++至少4.8版本。

- [**源码包地址**](https://gcc.gnu.org/releases.html)

- **安装**

  ```shell
  tar -xf gcc-10.1.0.tar.gz -C /usr/local/
  cd /usr/local/gcc-10.1.0
  
  # 下载安装gcc所需依赖包
  ./contrib/download_prerequisites
  
  mkdir gcc-build-10.1.0
  cd gcc-build-10.1.0
  
  # 配置
  ../gcc-10.1.0/configure --enable-checking=release --enable-languages=c,c++ --disable-multilib
  
  # 编译
  make
  
  # 安装
  make install
  
  # 重启操作系统
  ```

  > --enable-languages 用于设定 GCC 编译器支持编译的编程语言的类别，例如 c、c++、java、objc、obj-c++、go。

# gcc和g++

- **编译方式**

  > 得出结论：对于.c文件，gcc 指令以 C 语言代码对待，而 g++ 指令会以 C++ 代码对待。

  - *gcc*：根据程序文件的后缀名自动识别。

    > | 文件名称+扩展名                                              | GCC 编译器识别的文件类型                                     |
    > | ------------------------------------------------------------ | ------------------------------------------------------------ |
    > | file.c                                                       | 尚未经过预处理操作的 C 源程序文件。                          |
    > | file.i                                                       | 经过预处理操作、但尚未进行编译、汇编和连接的 C 源代码文件。  |
    > | file.cpp file.cp file.cc file.cxx file.CPP file.c++ file.C   | 尚未经过预处理操作的 C++ 源代码文件。                        |
    > | file.ii                                                      | 已经预处理操作，但尚未进行编译、汇编和连接的 C++ 源代码文件。 |
    > | file.s                                                       | 经过编译生成的汇编代码文件。                                 |
    > | file.h                                                       | C、C++ 或者 Objective-C++ 语言头文件。                       |
    > | file.hh file.H file.hp file.hxx file.hpp file.HPP file.h++ file.tcc | C++ 头文件。                                                 |

    - xxx.m：默认以编译 Objective-C 程序的方式编译此文件；
    - xxx.go：默认以编译 Go 语言程序的方式编译此文件；

  - *g++*：一律按照编译 C++ 代码的方式编译文件。

- **手动指定编译方式**：`-x` 参数。

  - -xc：c
  - -xc++：c++

- **gcc编译c++**：必须添加 `-lstdc++ -shared-libgcc` 链接c++标准库。

  > 等同于g++

# 编译

## 编译标准

> 不同版本的 GCC 编译器，默认使用的标准版本也不尽相同。可以手动控制。

```shell
gcc/g++ -std=编译标准
```

### C语言常用标准

> 表头：各个编译标准的名称。
>
> 内部：-std 可用的值。

| GCC 版本 \ 编译标准名称 | C89/C90   | C99             | C11             | C17     | GNU90       | GNU99             | GNU11             | GNU17       |
| ----------------------- | --------- | --------------- | --------------- | ------- | ----------- | ----------------- | ----------------- | ----------- |
| 10.1 ~ 8.4              | c89 / c90 | c99             | c11             | c17/c18 | gnu90/gnu89 | gnu99             | gnu11             | gnu17/gnu18 |
| 7.5 ~ 5.5               | c89/c90   | c99             | c11             |         | gnu90/gnu89 | gnu99             | gnu11             |             |
| 4.9.4 ~ 4.8.5           | c89/c90   | c99             | c11             |         | gnu90/gnu89 | gnu99             | gnu11             |             |
| 4.7.4                   | c89/c90   | c99（部分支持） | c11（部分支持） |         | gnu90/gnu89 | gnu99（部分支持） | gnu11（部分支持） |             |
| 4.6.4                   | c89/c90   | c99（部分支持） | c1x（部分支持） |         | gnu90/gnu89 | gnu99（部分支持） | gnu1x（部分支持） |             |
| 4.5.4                   | c89/c90   | c99（部分支持） |                 |         | gnu90/gnu89 | gnu99（部分支持） |                   |             |

### C++常用标准

| GCC 版本 \ 编译标准名称 | C++98/03    | C++11             | C++14             | C++17             | GNU++98         | GNU++11             | GNU++14             | GNU++17             |
| ----------------------- | ----------- | ----------------- | ----------------- | ----------------- | --------------- | ------------------- | ------------------- | ------------------- |
| 10.1 ~ 8.4              | c++98/c++03 | c++11             | c++14             | c++17             | gnu++98/gnu++03 | gnu++11             | gnu++14             | gnu++17             |
| 7.5 ~ 5.5               | c++98/c++03 | c++11             | c++14             | c++1z（部分支持） | gnu++98/gnu++03 | gnu++11             | gnu++14             | gnu++1z（部分支持） |
| 4.9.4 ~ 4.8.5           | c++98/c++03 | c++11             | c++1y（部分支持） |                   | gnu++98/gnu++03 | gnu++11             | gnu++1y（部分支持） |                     |
| 4.7.4                   | c++98       | c++11（部分支持） |                   |                   | gnu++98         | gnu++11（部分支持） |                     |                     |
| 4.6.4                   | c++98       | c++0x（部分支持） |                   |                   | gnu++98         | gnu++0x（部分支持） |                     |                     |
| 4.5.4                   | c++98       | c++0x（部分支持） |                   |                   | gnu++98         | gnu++0x（部分支持） |                     |                     |

## 编译过程

### 一步完成

> - 实际包含**4个过程**：预处理、编译、汇编和链接。
> - 一步完成不保留中间文件。

1. 编写 `demo.c` 文件。

   ```c
   #include <stdio.h>
   int main(){
      puts("GCC教程：http://c.biancheng.net/gcc/");
      return 0;
   }
   ```

2. 执行gcc命令。

   ```shell
   gcc demo.c
   ```

   > 默认生成 `a.out` 可执行文件。

3. 运行可执行文件。

   ```shell
   ./a.out
   ```

   > 输出：GCC教程：http://c.biancheng.net/gcc/。

### 分步编译

> 手动调用 GCC 编译器对 C、C++源代码的预处理、编译、汇编以及链接，每个阶段都会生成对源代码加工后的文件。

**源文件**

```c
#include <stdio.h>
int main(){
   puts("GCC教程：http://c.biancheng.net/gcc/");
   return 0;
}
```

#### 预处理

> - 处理源文件和头文件中以 # 开头的命令（比如 #include、#define、#ifdef 等），并删除程序中所有的注释 // 和 /* ... */。
>
> - 默认情况下 `gcc -E` 指令只会将预处理操作的结果输出到屏幕上，并不会自动保存到某个文件。
> - 通常用 ".i" 作为 C 语言程序预处理后所得文件的后缀名。

```shell
gcc -E -C demo.c -o demo.i
```

> [输出见](./gcc/demo.i)

##### 常用选项

| 选 项                       | 功 能                                                        |
| --------------------------- | ------------------------------------------------------------ |
| -C                          | **阻止** GCC **删除**源文件和头文件中的**注释**。            |
| -D name[=definition]        | 在处理源文件之前，先**定义宏 name**。宏 name 必须是在源文件和头文件中都没有被定义过的。将该选项搭配源代码中的#ifdef name命令使用，可以实现条件式编译。如果没有指定一个替换的值（即省略 =definition），该宏被定义为值 1。 |
| -U name                     | 如果在命令行或 GCC 默认设置中定义过宏 name，则**「取消」name 的定义**。-D 和 -U 选项会依据在命令行中出现的先后顺序进行处理。 |
| -include file               | 如同在源代码中添加 #include "file" 一样。                    |
| -iquote dir                 | 对于以引号（#include ""）导入的头文件中，-iquote 指令可以**指定该头文件的搜索路径**。当 GCC 在源程序所在目录下找不到此头文件时，就会去 -iquote 指令指定的目录中查找。 |
| -I dir                      | **同时适用于以引号 "" 和 <>** 导入的头文件。当 GCC 在 -iquote 指令指定的目录下搜索头文件失败时，会再自动去 -I 指定的目录中查找。该选项在 GCC 10.1 版本中已被弃用，并建议用 -iquote 选项代替。 |
| -isystem dir -idirafter dir | 都用于指定搜索头文件的目录，适用于以引号 "" 和 <> 导入的头文件。 |

- 指定 #include 搜索路径，作用先后顺序。
  - 首先搜索当前程序文件所在的目录，
  - 对于用 #include "" 引号形式引入的头文件，其次再前往 -iquote 选项指定的目录中查找；
  - 前往 -I 选项指定的目录中搜索；
  - 前往 -isystem 选项指定的目录中搜索；
  - 前往默认的系统路径下搜索；
  - 前往 -idirafter 选项指定的目录中搜索。

#### 编译

> - 将预处理得到的程序代码，经过一系列的词法分析、语法分析、语义分析以及优化，加工为当前机器支持的汇编代码。
>
> - 默认情况下，编译操作会自行新建一个文件名和指定文件相同、后缀名为 .s 的文件，并将编译的结果保存在该文件中。
> - 可以操作 .i 文件，也可以操作 .c 文件。

```shell
gcc -S demo.i -fverbose-asm
```

> - `-fverbose-asm` ：自行为汇编代码添加必要的注释。
>
> ```c
> 	.section	__TEXT,__text,regular,pure_instructions
> 	.build_version macos, 11, 1	sdk_version 11, 1
> 	.globl	_main                   ## -- Begin function main
> 	.p2align	4, 0x90
> _main:                                  ## @main
> 	.cfi_startproc
> ## %bb.0:
> 	pushq	%rbp
> 	.cfi_def_cfa_offset 16
> 	.cfi_offset %rbp, -16
> 	movq	%rsp, %rbp
> 	.cfi_def_cfa_register %rbp
> 	subq	$16, %rsp
> 	movl	$0, -4(%rbp)
> 	leaq	L_.str(%rip), %rdi
> 	callq	_puts
> 	xorl	%ecx, %ecx
> 	movl	%eax, -8(%rbp)          ## 4-byte Spill
> 	movl	%ecx, %eax
> 	addq	$16, %rsp
> 	popq	%rbp
> 	retq
> 	.cfi_endproc
>                                         ## -- End function
> 	.section	__TEXT,__cstring,cstring_literals
> L_.str:                                 ## @.str
> 	.asciz	"GCC\346\225\231\347\250\213\357\274\232http://c.biancheng.net/gcc/"
> 
> .subsections_via_symbols
> ```

#### 汇编：生成目标文件

> - **目标文件**：其本质为二进制文件，但由于尚未经过链接操作，所以无法直接运行。
> - 默认情况下，汇编操作会自行新建一个文件名和指定文件相同，后缀名为 .o 的文件。
> - 只需要根据汇编语句和机器指令的对照表一一翻译即可。

```shell
gcc -c demo.s
```

> [输出见](./gcc/demo.o)

- 如果指定文件为源程序文件（例如 demo.c），则 gcc -c 指令会对 demo.c 文件执行预处理、编译以及汇编这 3 步操作；
- 如果指定文件为刚刚经过预处理后的文件（例如 demo.i），则 gcc -c 指令对 demo.i 文件执行编译和汇编这 2 步操作；
- 如果指定文件为刚刚经过编译后的文件（例如 demo.s），则 gcc -c 指令只对 demo.s 文件执行汇编这 1 步操作。

#### **链接**

> 目标文件已经是二进制文件，与可执行文件的组织形式类似，只是有些函数和全局变量的地址还未找到，因此还无法执行。
>
> 链接的作用就是找到这些目标地址，将所有的目标文件组织成一个可以执行的二进制文件。

```shell
gcc demo.o -o demo
```

## 编译选项

| gcc/g++指令选项         | 功 能                                                        |
| ----------------------- | ------------------------------------------------------------ |
| -E（大写）              | 预处理指定的源文件，不进行编译。                             |
| -S（大写）              | 编译指定的源文件，但是不进行汇编。                           |
| -c                      | 编译、汇编指定的源文件，但是不进行链接。                     |
| -o                      | 指定生成文件的文件名。                                       |
| -llibrary（-I library） | 其中 library 表示要搜索的库文件的名称。该选项用于手动指定链接环节中程序可以调用的库文件。建议 -l 和库文件名之间不使用空格，比如 -lstdc++。 |
| -ansi                   | 对于 C 语言程序来说，其等价于 -std=c90；对于 C++ 程序来说，其等价于 -std=c++98。 |
| -std=                   | 手动指令编程语言所遵循的标准，例如 c89、c90、c++98、c++11 等。 |
| -save-temps             | 保留编译源文件过程中产生的所有中间文件。                     |

### o

```shell
gcc [-E|-S|-c] [infile] [-o outfile]
```

- **infile**：输入文件【要处理的文件】
  - 可以是源文件、汇编文件或者目标文件。
  - 可以放置多个文件，表示有多个输入文件。
- **outfile**：输出文件【处理的结果】，可以是预处理文件、目标文件、可执行文件等。

### l

> 链接器把多个二进制的目标文件（object file）链接成一个单独的可执行文件。
>
> 链接过程中，必须把符号【变量名、函数名等一些列标识符】用对应的数据的内存地址【变量地址、函数地址等】替代，以完成程序中多个模块的外部引用。

- **标准库**

  - 文件名称：libc.a【后缀`.a`代表「achieve」，译为「获取」】、libc.so【后缀`.so`代表「share object」，译为「共享对象」】。

  - 文件路径：一般位于 `/lib/` 或 `/usr/lib/`、或者位于 [GCC](http://c.biancheng.net/gcc/) 默认搜索的其他目录。

  - 默认链接。

    - 标准头文件 [math.h](http://c.biancheng.net/ref/math_h/) 对应的数学库默认也不会被链接，需要手动添加。

      ```shell
      gcc main.c -o main.out -lm
      ```

      > 数学库的文件名是 libm.a。前缀`lib`和后缀`.a`是标准的，`m`是基本名称。

- **非标准库**

  - 需要手动添加。

  - 链接其它目录中的库：

    - 链接库作为一般的目标文件，为 GCC 指定该链接库的完整路径与文件名。

      ```shell
      gcc main.c -o main.out /usr/lib/libm.a
      ```

    - 使用`-L`选项，为 GCC 增加另一个搜索链接库的目录。

      ```shell
      gcc main.c -o main.out -L/usr/lib -lm
      ```

      > -L：使用冒号分割的路径列表。

    - 把包括所需链接库的目录加到环境变量 LIBRARYPATH 中。

## 编译多文件

- **处理多个文件**

  ```shell
  gcc -c demo1.c demo2.c
  ```

  > 生成两个.o文件。
  >
  > ```shell
  > gcc -c demo1.c demo2.c -o demo1.o demo2.o
  > ```
  >
  > 不允许。

  - 可共用gcc的操作。
    - 将多个 C（C++）源文件加工为汇编文件或者目标文件；
    - 将多个 C（C++）源文件或者预处理文件加工为汇编文件或者目标文件；
    - 将多个 C（C++）源文件、预处理文件或者汇编文件加工为目标文件；
    - 同一项目中，不同的源文件、预处理文件、汇编文件以及目标文件，可以使用一条 gcc 指令，最终生成一个可执行文件。
  - 不可以使用 `-E` 生成预处理文件。

- **编译多个文件**

  - 先编译，再链接。

    ```shell
    gcc -c myfun.c main.c
    gcc myfun.o main.o -o main.exe
    ```

  - 直接编译并链接。

    ```shell
    gcc myfun.c main.c -o main.exe
    ```

  - 匹配编译

    ```shell
    gcc *.c -o main.exe
    ```

# 链接库

> - 头文件只存储变量、函数或者类等这些功能模块的声明部分。
>
> - 库文件负责存储各模块具体的实现部分。
>
> - 库文件无法直接使用，只能通过头文件间接调用。

## 静态链接库

- **实现**：用到库文件的功能模块时，编译器将该模板代码直接复制到程序文件的适当位置，最终生成可执行文件。

- **文件名称**：后缀名通常用 .a 表示。

- **优势**：生成的可执行文件不再需要任何静态库文件的支持就可以独立运行。【可移植性强】
- **劣势**：如果程序文件中多次调用库中的同一功能模块，则该模块代码势必就会被复制多次，生成的可执行文件中会包含多段完全相同的代码，造成代码的冗余。

### 创建

1. **生成编译文件**

   ```shell
   gcc -c sub.c add.c div.c
   ```

2. **使用 ar 压缩指令，将生成的目标文件打包成静态链接库**

   ```shell
   ar rcs libmymath.a add.o sub.o div.o
   ```

   > - 基本格式：`ar rcs 静态链接库名称 目标文件1 目标文件2 ...`。
   > - 静态链接库命名规范：`libxxx.a`。

### 使用

- static

  ```shell
  gcc -c main.c
  gcc -static main.o libmymath.a
  ```

- -L

  ```shell
  gcc -c main.c
  gcc main.o -L {路径} -lmymath
  ```

## 动态链接库

- **实现**：用到库文件的功能模块时，将功能模块的位置信息记录到文件中，直接生成可执行文件。

- **文件名称**：后缀名通常用 .so 表示。

- **优势**：即便功能模块被调用多次，使用的都是同一份实现代码。
- **劣势**：生成的可执行文件无法独立运行，必须借助相应的库文件。【可移植性差】

### 创建

- **直接使用源文件创建动态链接库**

  ```shell
  gcc -fpic -shared add.c sub.c div.c -o libmymath.so
  ```

  > - 格式：`gcc -fpic -shared 源文件名... -o 动态链接库名`。
  > - -shared：生成动态链接库。
  > - -fpic：目标文件中函数、类等功能模块的地址使用相对地址，而非绝对地址。

- **先生成编译文件，再创建动态链接库**

  ```shell
  gcc -c -fpic add.c sub.c div.c
  gcc -shared add.o sub.o div.o -o libmymath.so
  ```

### 使用

```shell
gcc main.c libmymath.so -o main
```

