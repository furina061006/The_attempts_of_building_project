# 学习感悟
## 目录
- [学习感悟](#学习感悟)
  - [目录](#目录)
  - [项目和工程](#项目和工程)
    - [项目：](#项目)
    - [工程：](#工程)
    - [模块化思想](#模块化思想)
  - [include 预处理指令](#include-预处理指令)
    - [#include\<\> 和 #include ""](#include-和-include-)
      - [共同作用：](#共同作用)
      - [区别：](#区别)
      - [include \<header.h\>的查找位置：](#include-headerh的查找位置)
  - [编译过程里的密码](#编译过程里的密码)
    - [C程序从生到跑有六大过程:](#c程序从生到跑有六大过程)
    - [Q: 链接时发生了什么？](#q-链接时发生了什么)
    - [A :](#a-)
        - [C 程序内存布局：`.text`、`.data` 与 `.bss` 段了](#c-程序内存布局textdata-与-bss-段了)
        - [主要内存段概览](#主要内存段概览)
    - [被调C文件与被调头文件](#被调c文件与被调头文件)
      - [作用阶段和各自功能](#作用阶段和各自功能)
      - [被调C文件和被调头文件分离的好处](#被调c文件和被调头文件分离的好处)
      - [头文件保护](#头文件保护)
  - [gcc编译器(GNU-C/C++-compiler)](#gcc编译器gnu-cc-compiler)
    - [gcc编译器的常见参数](#gcc编译器的常见参数)
      - [基础编译控制](#基础编译控制)
      - [高阶用法](#高阶用法)
  - [工程化的结构](#工程化的结构)
  - [常见的三种编译方式](#常见的三种编译方式)
  - [makefile 的使用](#makefile-的使用)

## 项目和工程
### 项目：
项目就是完成一件事情的所有过程的总和。比如野生动物保护项目，C语言大作业项目等等，前面的头衔是**预期结果**，而项目就是为了完成这个预期结果的`**行动总和**。
### 工程：
工程是工业化追求**规范性**的产物，是一种方法论。在组织项目时，有的方法成功率高
>（指的不仅是把一个东西设计出来就完事了），一个真正的项目不仅涉及设计，还涉及优化和维护，甚至是后来者的交互，只有这一切都做到很好，效率高，才能叫做**成功**

而有的成功率低。把成功率高的方法总结成方法论，便是一个工程化的过程。
工程化的好处个人认为，部分体现在规范性带来的高效率。比如一个极其简陋的一个项目部分组成：
>![alt text](./0.png)
如果不对各类文件进行管理，到时候回过头来优化维护很很伤头脑

同时，又比如main.c文件，他大可以不叫main.c，随便什么名字都行，但是main.c是约定俗成的，能让其他人更容易理解和快速上手，避免在这种地方浪费时间适应，而且如果每个人都这样干，那肯定会记混，同样影响效率。
### 模块化思想
为什么要把一个大文件拆分成若干小的C语言文件呢？
在一个操作系统内核里，代码会有数十万行，如果此时将C代码全放在一个源文件内，就会显得很臃肿，代价就是其他人甚至你自己日后可能都不知道这堆💩山是什么意思，想查找相关函数功能，还要找半天（当然可以转到函数定义，但是跳转回来又是个问题）但肯定会更难看些进而影响了优化以及后期维护的难度。
把一个大文件拆分成多个按功能划分的小的C语言文件，不仅可以提高优化效率，而且通过头文件和源文件，函数声明和实现相分离，在进行调试时能只再次编译改动部分的小文件，大大减少编译时间。

## include 预处理指令
### #include<> 和 #include ""
#### 共同作用：
告诉编译器（如 GCC）从 系统，标准库，源文件目录甚至指定的环境变量路径的头文件路径 中查找并包含指定的头文件。
#### 区别：
| 写法 | 名称 | 查找顺序 |
|------|------|--------|
| `#include <stdio.h>` | 尖括号形式 | 先查系统/标准库路径 |
| `#include "my_header.h"` | 双引号形式 | 先查**当前源文件所在目录**，再查系统路径 |
#### include <header.h>的查找位置：
- **标准库目录（如 /usr/include/）**
```bash
ls /usr/include | grep -E "^*.h"
# 左边输入/usr/include 目录的所有文件，然后右边输出左边输入文件中所有.h文件。
```
更仔细的右边部分讲解：
| 部分 | 含义 |
|------|------|
| `grep` | 全局搜索正则表达式并打印（Globally search for a Regular Expression and Print） |
| `-E` | 使用 扩展正则表达式（Extended Regular Expressions），支持 `|`（或）、`()` 分组等语法 |
| `"..."` | 引号内的内容是正则表达式模式 |    
| `"^"`  | 表示行的开头（anchor），确保按后面的字符从行首开始匹配 |
| `*` | 一个分组，比如 [1] 表示匹配以下所有的任意一个开头的头文件：<br>• `stdio`<br>• `stdlib`<br>• `string`<br>• `math`<br>• `time` |
| `.h` | 匹配字面量 `.h`（注意：在正则中 `.` 默认匹配任意字符，但这里因为是 `.h` 且没加反斜杠，在大多数情况下仍能工作；更严谨应写成 `\.h`，但实践中常省略） |
> [1]的意思是(stdio|stdlib|string|math|time)，由于表格的语法无法写进去

- **编译器内置路径**
>GCC 默认会搜索：
/usr/include
/usr/local/include
/usr/lib/gcc/x86_64-linux-gnu/11/include（版本路径）
其他平台相关路径
- **通过 -I 选项指定的额外系统级头文件目录**
(显式指定路径，用 CMake/Makefile 管理的时候整挺好)
```bash
gcc -I/opt/mylib/include -I./headers main.c ....-c ....-o .....
# 这种方法只在本次编译生效
```
1. -I/path 会把 /path 加入 #include <...> 和 #include 
2. "..." 的搜索路径。多个 -I 按从左到右优先级排序（左边优先）。
- **环境变量（难以复现）**
虽然没有直接叫 INCLUDE_PATH 的标准环境变量（像 Windows 那样），但以下环境变量会影响头文件搜索：

| 环境变量 | 作用 |
|--------|------|
| `CPATH` | 对 GCC 有效：相当于给所有编译加了 `-I`<br>例：`export CPATH=/my/include:$CPATH` |
| `C_INCLUDE_PATH` | 仅用于 C 语言的 `#include <...>` |
| `CPLUS_INCLUDE_PATH` | 仅用于 C++ 的 `#include <...>` |

## 编译过程里的密码
### C程序从生到跑有六大过程:

**前四个阶段合起来叫 “构建”（`Build`），结果是一个可执行文件**

| 阶段 | 工具 | 输入->输出 | 作用 |
|---|---|---|---|
|预处理（Preprocessing）| cpp |  ``.c`` → `.i`|处理 #include、#define、条件编译等,然后将预处理文件内的所有信息复制到相应行上 |
编译（Compilation）|cc1|`.i` → `.s`|把 C 代码翻译成汇编语言|
汇编（Assembly）|as|`.s` → `.o`|把汇编转成机器码（目标文件，含符号但未定位）|
链接（Linking）|ld|`.o` + 库 → 可执行文件（如 `a.out`）|合并多个 .o，解析符号引用，分配最终地址|

**自此,  C源程序文件转为可执行文件**
**后两个阶段是运行阶段：**
|阶段|	谁负责	|发生时机|	关键动作|
|---|---|---|---|
加载|	操作系统|	运行时|	把可执行文件装入内存，准备执行
运行|
***
### Q: 链接时发生了什么？
### A :
> 链接器为**每个函数/全局变量**分配最终的虚拟地址（在可执行文件中固定）
> 把多个 `.o` 文件中**相互引用的符号**（如函数、变量）进行**地址绑定**，解决“谁调用了谁”的问题，最终生成可执行文件。
> **地址绑定**: **相互引用的符号**都会分配同一`text`段地址
> 那是**段**是什么意思呢？
> 这就要谈到

##### C 程序内存布局：`.text`、`.data` 与 `.bss` 段了

当你编译并运行一个 C 程序，操作系统会将其加载到内存，并划分为几个关键区域。理解这些段（segments/sections）对调试、优化和嵌入式开发至关重要。

#####  主要内存段概览
(内存从低到高)

| 段名 | 全称 | 作用 | 权限 |
|------|------|------|------|
| `.text` | 代码段（Text Segment） | 存放程序的机器指令（函数代码） | **只读 + 可执行** |
| `.data` | 已初始化数据段 | 存放已初始化的全局变量和静态变量 | 可读 + 可写 |
| `.bss` | 未初始化数据段（Block Started by Symbol） | 存放未初始化或初始化为 0 的全局/静态变量 | 可读 + 可写（运行前清零） |
| **堆（Heap）** | — | 动态分配内存（`malloc`、`new`） | 可读写，运行时增长 |
| **栈（Stack）** | — | 局部变量、函数参数、返回地址等 | 可读写，自动管理 |

>  `.text`、`.data`、`.bss` 是 **静态内存布局**，由编译器和链接器在构建阶段确定。

---
### 被调C文件与被调头文件
#### 作用阶段和各自功能
大家可以看一下./project:
c.h:
```c
#include <stdio.h>
#include "b.h"

void c();
```
c.c:
```c
#include <stdio.h>
#include "c.h"
void c()
{
        printf("c\n");
}
```
可以见得，c.h里放的只有预处理指令和函数声明；
而c.c里放的是函数的实现
**那，这有什么好处呢？**
我们来分析一下他们发挥功能的时期：
- **c.h 头文件：**
主要在 `预处理阶段` 生效
>过程：
#include "c.h"（或其它文件包含它）会在预处理阶段被展开。
预处理器会将 c.h 的全部内容原样复制到包含它的 .c 文件中。
同时，c.h 中的 #include <stdio.h> 和 #include "b.h" 也会在此阶段递归展开。

>作用：
提供函数声明（如 void c();），让编译器知道 c() 的存在和签名。
如果没有这个声明，调用 c() 时可能产生隐式声明警告（C89 允许但危险，C99+ 报错）。
总而言之，c.h 在预处理阶段被插入，在编译阶段被用于类型/函数检查。

一个小补充：一些编译器（比如gcc）在进行编译的时候，由于性能问题，从上到下依此编译，一次编译三四行且不回头。
众所周知，一个函数想要被调用，就首先需要被声明，想要声明成功，就要有函数的定义。
**那，如果函数声明在函数定义前面，那该怎么办呢？**
聪明的编译器马上想到了先在`声明`处打上标记，如果后期出现了`定义`，那就把标记取消，不进行报错。
**那，如果一个项目里不小心有两个相同的函数定义，那该怎么办呢？**
- `使用 static 关键字（内部链接）`
```c
// file1.c
static void helper() { ... }  // 只在本文件可见,外部无法看见

// file2.c
static void helper() { ... }  // 没问题！名字相同但互不影响
```
- `用头文件保护 + 声明/定义分离`
函数声明放在 .h 中（用 extern 或直接写 void foo();）
函数定义只放在一个 .c 中
其他文件通过 #include "xxx.h" 使用
这种方法就是我们头文件和源文件分离的部分原因了
- `条件编译`
这个模块有点大，想看的先跳转到[头文件保护](#头文件保护)


---

- **c.c源程序文件**
*四个阶段都有参与*
>预处理
展开它自己包含的头文件
处理宏、条件编译等。

>编译（→ 汇编代码）
编译器将 void c(){ printf("c\n"); } 转换成汇编指令。
此时需要知道 printf 的声明（来自 <stdio.h>，已在 c.h 中包含）。

>汇编（→ 目标文件 c.o）
汇编器将汇编代码转为机器码，生成 c.o（包含符号 c 的定义）。

>链接
当其他 .o 文件（比如 main.o）调用 c() 时，链接器会从 c.o 中找到 c 的实现并连接。
同时链接 printf 对应的 C 标准库（如 libc）。

所以，c.c 是实现体，最终通过链接阶段提供函数定义。
而前面三步与main.o保持独立关系。
#### 被调C文件和被调头文件分离的好处
- 声明和实现的分离，即文件物理（地域？）和逻辑上的分离，美观易管理
- 首先，我们要知道一件事：
  如果更改被调函数的其中一个，则不需要完全重新编译，编译器（如gcc）会自动通过对比每个.c文件的哈希时间戳和对应.o哈希时间戳（暂且这样叫一下）判断.c文件是否有更改，如果有,那么就再次编译该被调函数的源程序文件。其余的并不会再次编译。
如果没有头文件，a.c里直接是：
```c
#include <stdio.h>
#include "a.c"//直接放源程序文件
int main()
{
    a();
    b();
    c();
}

void a()
{
    printf("a\n");
}
```
这会导致在预处理阶段，他们就绑定在一起，最终的目标文件也只有a.o一个，c.c源文件改变，导致从而导致重新编译时带着a.c文件一起被编译，编译时间增加。
而如果有头文件，那么各自编译各自的（.c->.i->.s->.o），最后一步再陕北会师(.o->.exe/out)。这极大减少了调试时反复编译而浪费的时间


#### 头文件保护
手段： `条件编译` 或  `#pragma once`
- **条件编译**
```c
// c.h
#ifndef C_H
#define C_H
/*头文件内容
void foo();  // 声明 OK
int x;       // 定义整形变量x
            //千万不要在这里写函数定义（除非是 inline/static）
*/
#endif
```
这样做可以避免出现重复定义
除此之外，条件编译还可以用在
1.平台/配置相关的不同实现（同一函数，不同版本）
```c
// utils.c
#ifdef _WIN32
void platform_init() {
    // Windows 实现
}
#elif __linux__
void platform_init() {
    // Linux 实现
}
#endif
```
2.调试 vs 发布版本上
```c
// utils.c
#ifdef _WIN32
void platform_init() {
    // Windows 实现
}
#elif __linux__
void platform_init() {
    // Linux 实现
}
#endif
```
具体原理还没看懂，有空再回头看看
- **现代替代方案 #pragma once**
很多编译器（GCC、Clang、MSVC）支持：
```c
// mylib.h
#pragma once   // ← 告诉预处理器：这个文件只包含一次

void foo();
```
更简洁，不需要写 #define MYLIB_H
但 不是 C/C++ 标准（尽管广泛支持）
在某些复杂场景（如符号链接、网络路径）可能不如传统 guard 可靠
所以很多项目仍使用传统的 #ifndef / #define 方式。
## gcc编译器(GNU-C/C++-compiler)
### gcc编译器的常见参数
有两个了解途径：
1.bash里问`man gcc`
2.问AI（确实好用啊）
问`man gcc`后，有
![alt text](./1.png)
有需要查询就可以
但我认为初学者还是问AI来的快且好理解，以下是部分常见参数
#### 基础编译控制
| 选项 | 全称 / 含义 | 说明 |
|------|-------------|------|
| `-c` | compile only（只编译） | 只进行预处理、编译、汇编，生成 `.o` 目标文件，不链接 |
| `-S` | assemble only（只到汇编） | 编译到汇编代码（`.s` 文件），不汇编也不链接 |
| `-E` | preprocess only（只预处理） | 仅运行预处理器，输出 `.i` 文件（展开 `#include`、`#define` 等） |
| `-o file` | output（输出文件） | 指定输出文件名及其后缀，他就是个改名字的，输出文件的类型与他无关，输出`可执行文件`那你就加`.out`、`目标文件`那你就加`.o` |
|-g|GDB调试|紧跟gcc 后面|
示例：
```bash
# 1.最简单的编译
gcc hello.c  ~(等价)  gcc -o a.out hello.c
# 默认输出a.out

# 2.指定输出文件名
gcc hello.c -o hello #-o 是“选项”，不是“参数顺序敏感”的命令,放在前面和后面都可以，当然后面更美观些

# 3.分步编译（预处理 → 编译 → 汇编 → 链接）
gcc -E hello.c -o hello.i
gcc -S hello.c -o hello.s
gcc -c hello.c -o hello.o
gcc hello.o -o hello

# 4.多文件编译
gcc main.c utils.c -o program #两个文件最终编译成一个可执行文件
# 所有中间产物（如临时 .o 文件）在内存或临时目录中生成后立即删除
# 或分步编译
gcc -c main.c -o main.o       
gcc -c utils.c -o utils.o
gcc main.o utils.o -o program   #.o 文件作为文件保留在当前目录

# 5.启用警告（强烈推荐）
gcc -Wall -Wextra hello.c -o hello
# -Wall：启用常见警告
# -Wextra：启用额外警告

# 6.调试信息（用于 gdb）
gcc -g hello.c -o hello  #生成带调试符号的可执行文件，可用 gdb ./hello 调试
```
其中，`4.多文件编译`里谈到了`分步编译`，他留有.o的特性，让他在某些情况下有一定的优势：
- **加快后续编译速度（增量编译）**

如果你只修改了 utils.c，那么下次只需：
```c
gcc -g -c utils.c -o utils.o      # 重新编译 utils.c
gcc main.o utils.o -o program     # 链接（main.o 不用重编）

```
而一步编译每次都要重新处理所有源文件,费时巨大。
这同时也是 `make` 工具的核心思想：只重新编译改动过的文件
- **复杂项目管理**
大型项目有几十上百个 .c 文件，不可能每次都全量编译。
分别编译成 .o 后，可以按模块组织、并行编译、静态库打包等。
- **特殊编译需求**
某些文件需要不同的编译选项：
```bash
gcc -g -O0 -c debug_module.c -o debug_module.o    # 关闭优化便于调试
gcc -g -O2 -c fast_math.c -o fast_math.o         # 开启优化
gcc debug_module.o fast_math.o -o program
```
---
#### 高阶用法
- **开启优化**
```bash
gcc -Ox hello.c -o hello #那个是大O，不是0
```
| 选项 | 全称 / 含义 | 说明 |
|------|-------------|------|
| `-g` | debug info | 生成调试信息，供 `gdb` 使用 |
| `-O0` | Optimization level 0 | 不优化（默认，便于调试） |
| `-O1`, `-O2`, `-O3` | Optimization level 1/2/3 | 逐步提高优化级别（速度 vs 体积） |
| `-Os` | Optimize for size | 优化代码大小（适合嵌入式） |
| `-Ofast` | Fast optimization | 在 `-O3` 基础上允许不严格遵循标准（更快但可能不精确） |


---
- **指定 C 标准版本和预警**
```bash
gcc -std=c99 hello.c -o hello      # C99 标准
gcc -std=c11 hello.c -o hello      # C11
gcc -std=c17 hello.c -o hello      # C17（也写作 c18）
gcc -std=gnu11 hello.c -o hello    # GNU 扩展 + C11
```
| 选项 | 全称 / 含义 | 说明 |
|------|-------------|------|
| `-Wall` | Warn all (common warnings) | 启用大多数常用警告（如未使用变量、格式不匹配等） |
| `-Wextra` | Extra warnings | 启用更多额外警告（比 `-Wall` 更严格） |
| `-Werror` | Warnings as errors | 将所有警告视为错误（强制修复） |
| `-Wpedantic` | Strict ISO C/C++ compliance | 对非标准扩展发出警告 |
| `-std=c99` / `-std=c11` / `-std=c++17` | Standard | 指定语言标准（C99、C11、C++17 等） |


- **链接数学库（如使用 sin, cos, sqrt 等）**
```bash
gcc math_example.c -o math_example -lm
```
-lm 表示链接 libm.so（数学库）
注意：-lm 通常放在命令末尾（链接顺序很重要）
- **查看预定义宏**（暂时看不懂）
```bash
gcc -dM -E - < /dev/null | grep "__GNUC__"
# 或查看全部：
gcc -dM -E - < /dev/null
```
| 选项 | 全称 / 含义 | 说明 |
|------|-------------|------|
| `-D NAME` | Define macro | 定义宏 `NAME`，相当于 `#define NAME 1` |
| `-D NAME=VALUE` | Define with value | 定义带值的宏，如 `-DDEBUG=1` |
| `-U NAME` | Undefine macro | 取消已定义的宏 `NAME` |


- **静态链接**
```bash
gcc -static hello.c -o hello_static
```
生成完全静态链接的可执行文件（不依赖动态库）
文件较大，但可移植性强
- **只检查语法（不生成文件）**
```bash
gcc -fsyntax-only hello.c
# 仅检查语法错误，不生成任何输出文件
```
- **头文件与库路径**

| 选项 | 全称 / 含义 | 说明 |
|------|-------------|------|
| `-I dir` | Include directory | 添加头文件搜索路径（用于 `#include <...>`） |
| `-L dir` | Library directory | 添加库文件搜索路径（链接时找 `.a` 或 `.so`） |
| `-l name` | Library name | 链接 名为 `libname.a` 或 `libname.so` 的库 |
```bash
gcc main.c -I ./headers -L ./lib -l mylib -lm -o program
```
会链接 ./lib/libmylib.a 和系统数学库 libm.so
## 工程化的结构
这一块不太了解，先上AI的tree看看：
- 大工程结构：
my_project/
├── CMakeLists.txt          # 或 Makefile（构建配置入口）
├── README.md               # 项目说明
├── LICENSE                 # 开源许可证（如有）
├── .gitignore              # Git 忽略文件
│
├── src/                    # 源代码目录（核心逻辑）
│   ├── main.c              # 程序入口
│   ├── core/
│   │   ├── parser.c
│   │   └── parser.h
│   └── utils/
│       ├── logger.c
│       └── logger.h
│
├── include/                # 公共头文件（供外部或内部引用）
│   └── mylib.h             # 主头文件（可选）
│
├── lib/                    # 第三方静态库或动态库（可选）
│   ├── libfoo.a
│   └── libbar.so
│
├── third_party/            # 第三方源码依赖（如 json-c, zlib）
│   └── cJSON/
│       ├── cJSON.c
│       └── cJSON.h
│
├── build/                  # 构建输出目录（通常不提交到 Git）
│   ├── CMakeCache.txt
│   ├── Makefile
│   └── my_app              # 最终可执行文件
│
├── tests/                  # 单元测试 / 集成测试
│   ├── test_parser.c
│   └── CMakeLists.txt
│
├── docs/                   # 文档（设计文档、API 说明等）
│   └── design.md
│
└── scripts/                # 辅助脚本（构建、部署、格式化等）
    ├── build.sh
    ├── format.sh           # clang-format 自动格式化
    └── run_tests.sh
- 动态库和静态库的区别

| 特性 | 静态库（`.a`） | 动态库（`.so`） |
|------|----------------|----------------|
| 链接时机 | 编译时（链接阶段） | 运行时 |
| 可执行文件大小 | 大（包含库代码） | 小（只含引用） |
| 运行依赖 | 无 | 需要 `.so` 文件存在 |
| 内存占用 | 每个进程一份 | 所有进程共享一份 |
| 更新方式 | 必须重新编译程序 | 替换 `.so` 即可 |
| 典型用途 | 嵌入式、安全敏感、独立分发 | 操作系统库（如 glibc）、插件系统 |
> 库是什么？
提供函数/变量的完整实现（声明在头文件里）
是已编译好的二进制代码，可以当成一个已经编译完成的目标文件，只差链接在一起了。
## 常见的三种编译方式
根据 是否**保留中间文件**、**是否分步操作**、**是否使用构建工具** 而划分的
- **1.直接一步编译（All-in-One Compilation）**
小型程序（1～2 个源文件）
快速测试、学习、脚本化任务
```bash
gcc main.c utils.c -o program
```
特点：
简单快捷：一条命令完成预处理、编译、汇编、链接。
不保留中间文件（如 .o、.s）。

缺点：
每次修改任一源文件，全部重新编译,无法增量编译（大项目慢）
不便于管理复杂依赖
- **2.分步手动编译（Manual Multi-Step Compilation）**
```bash
# 1. 编译成目标文件（可保留 .o）
gcc -c main.c -o main.o
gcc -c utils.c -o utils.o

# 2. 链接生成可执行文件
gcc main.o utils.o -o program
```
特点：
显式控制每个阶段
保留 .o 文件，便于增量编译（只重编修改过的 .c）
可对不同源文件使用不同选项
缺点：
手动操作繁琐，容易出错
不适合大型项目（几十上百个文件）
- **3.使用构建工具自动编译（Automated Build with Make / CMake）**
```bash
make        # 自动编译（只重编改动的文件）
make clean  # 清理
```
优点：（加一层）
自动依赖分析：只重新编译被修改的文件及其依赖。
高度可维护：规则写一次，反复使用。
支持复杂逻辑：条件编译、多平台适配、安装规则等。
工业标准：几乎所有开源 C/C++ 项目都用 Make、CMake、Meson 等。

## makefile 的使用
```makefile
OUT?=./builds   #设变量
TARGET?=main

all: build    # ：后面是依赖，想要all执行，必须先有build,不然就得先转向build并执行。
	@gcc $(OUT)/*.o -o $(OUT)/$(TARGET)
	@$(OUT)/$(TARGET)   
    # $(OUT)相当于把OUT的value输出
$(OUT):
	@mkdir -p $(OUT)

build: $(OUT)
	@gcc -I./headers -c ./srcs/a.c -o $(OUT)/a.o
	@gcc -I./headers -c ./srcs/b.c -o $(OUT)/b.o
	@gcc -I./headers -c ./srcs/c.c -o $(OUT)/c.o


clean:
	@rm -rf $(OUT)

.PHONY: all build clean   #避免把这些当成文件名或文件夹
```
>`@`是隐写的意思，以下是有无`@`在bash 下的输出
```bash
# 有@
a
b
c

# 无@
mkdir -p ./builds
gcc -I./headers -c ./srcs/a.c -o ./builds/a.o
gcc -I./headers -c ./srcs/b.c -o ./builds/b.o
gcc -I./headers -c ./srcs/c.c -o ./builds/c.o
gcc ./builds/*.o -o ./builds/main
./builds/main   #把所有命令行都输出在终端上，一般调试的时候用
a
b
c
```
