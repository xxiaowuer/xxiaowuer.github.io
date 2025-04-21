---
title: GDB Tutorial
date: 2024-05-23 16:07:59 +0800
categories: [TOOL, GDB]
tags: [gdb, tutorial]     # TAG names should always be lowercase
---

## GDB version

> gdb -v

``` shell
xiaowu@DESKTOP-FE78O6H:~$ gdb -v
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
xiaowu@DESKTOP-FE78O6H:~$ 
```

## Debugging procedure

- 要进入gdb开始调试，直接`gdb+可执行程序`即可
- 退出gdb可以使用`q(quit)`

### The difference between [Debug] and [Release] version

Debug：调试版本

Release：发布版本

- 当程序员自己测试完没有问题之后，就会将这个可执行程序给到`测试人员`进行测试，而且会给出自己的单元测试报告。对**于测试人员来说所处的模式是【Release】**，也就是将来客户要使用的这款软件的发布版本

直接使用make生成的可执行程序是通过gcc编译得到的，它是一个【Release】版本的可执行程序，因此无法进行调试。

- 修改命令使其生成【Debug】版本的可执行程序

> gcc -o mytest-debug test.c -g

加入`-g`就可以生成调试版本的可执行程序

## Common commands

``` shell
命令               简写形式         说明
backtrace          bt、where       显示backtrace
break              b               设置断点
continue           c、cont         继续执行
delete             d               删除断点
finish                             运行到函数结束
info breakpoints                   显示断点信息
next               n               执行下一行
print              p               显示表达式
run                r               运行程序
step               s               一次执行一行，包括函数内部
x                                  显示内存内容
until              u               执行到指定行

其他命令
directory          dir             插入目录
disable            dis             禁用断点
down               do              在当前调用的栈帧中选择要显示的栈帧
edit               e               编辑文件或者函数
frame              f               选择要显示的栈帧
forward-search     fo              向前搜索
generate-core-file gcore           生成内核转存储
help                h              显示帮助一览
info                i              显示信息
list                l              显示函数或行
nexti               ni             执行下一行(以汇编代码为单位)
print-object        po             显示目标信息
sharelibrary        share          加载共享的符号
stepi               si             执行下一行
```

## Debugging start

### Demonstrating program

使用多进程计算质数的程序**process6.c**

``` c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

#define LEFT    30000000
#define RIGHT   30000200
#define N       3

int main() {
    pid_t pid;
    int i, j, n, mark;

    for (int n = 0; n < N; n++) {
        pid = fork();
        if (pid < 0) {
            perror("fork()");
            exit(1);        // 这里直接退出有些问题，可能有些子进程还没来的及回收 
        }

        if (pid == 0) {
            for (i = LEFT + n; i <= RIGHT; i += N) {
                mark = 1;
                for (j = 2; j < i / 2; j++) {
                    if (i % j == 0) {
                        mark = 0;
                        break;
                    }
                }
                if (mark) {
                    printf("pid:%d calculate %d is a primer\n", getpid(), i);
                }
            }
            exit(0);
        }
    }
    
    for (n = 0; n < N; n++) {
        wait(NULL);
    }

    exit(0);
}
```

### compile to a debuggable executable file

``` shell
xiaowu@DESKTOP-FE78O6H:~/linux-review/process$ ls
process6  process6.c  test
xiaowu@DESKTOP-FE78O6H:~/linux-review/process$ gcc -o test-debug process6.c -g
xiaowu@DESKTOP-FE78O6H:~/linux-review/process$ ls
process6  process6.c  test  test-debug
xiaowu@DESKTOP-FE78O6H:~/linux-review/process$ gdb test-debug 
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from test-debug...
(gdb) 
```

## Commands

### View sr-code and asm-code

- `layout split` 这是源代码和汇编代码都显示
- `layout src` 只显示源代码
- `layout asm` 只显示汇编代码

### Show code

- `l (list) <line number> / <function name>`

显示相应的code，每次10行

> l + 行号

``` shell
(gdb) l 23
18                  perror("fork()");
19                  exit(1);        // 这里直接退出有些问题，可能有些子进程还没来的及回收 
20              }
21
22              if (pid == 0) {
23                  for (i = LEFT + n; i <= RIGHT; i += N) {
24                      mark = 1;
25                      for (j = 2; j < i / 2; j++) {
26                          if (i % j == 0) {
27                              mark = 0;
(gdb) 
```

> l + 函数名

``` shell
(gdb) l main
6
7       #define LEFT    30000000
8       #define RIGHT   30000200
9       #define N       3
10
11      int main() {
12          pid_t pid;
13          int i, j, n, mark;
14
15          for (int n = 0; n < N; n++) {
(gdb) 
```

想要继续往下显示的话，直接按回车键`Enter`就可以，**gdb会自动记忆上次输入的****指令**

- 如果直接使用`L`而不加后面的参数，会随机显示该源文件中的10行内容

``` shell
(gdb) l
1       #include <stdio.h>
2       #include <stdlib.h>
3       #include <sys/types.h>
4       #include <sys/wait.h>
5       #include <unistd.h>
6
7       #define LEFT    30000000
8       #define RIGHT   30000200
9       #define N       3
10
(gdb) 
```

### Set breakpoint：

- `b <line number>`:在那一行打上断点

``` shell
(gdb) b 15
Breakpoint 3 at 0x11f5: file process6.c, line 15.
(gdb) 
```

- `b <file name>:<function name>`：在该文件中该函数的第一行打上断点

``` shell
(gdb) b process6.c:main
Note: breakpoint 3 also set at pc 0x11f5.
Breakpoint 5 at 0x11f5: file process6.c, line 15.
(gdb) 
```

- `b <file name>:<line number>`：在该文件的那一行打上断点

``` shell
(gdb) b process6.c:19
Breakpoint 4 at 0x121e: file process6.c, line 19.
(gdb) 
```

- `b *0x34` 在某一个地址上打上断点，这里是在0x34位置打上断点，注意要加*号 

### Check breakpoint infomation

- `info b`：查看所打断点的信息

``` shell
(gdb) info b
Num     Type           Disp Enb Address            What
3       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
4       breakpoint     keep y   0x000000000000121e in main at process6.c:19
5       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
(gdb) 
```

若是直接输入`info`，将会打印所有的调试信息

``` shell
(gdb) info
List of info subcommands:

info address -- Describe where symbol SYM is stored.
info all-registers -- List of all registers and their contents, for selected stack frame.
info args -- All argument variables of current stack frame or those matching REGEXPs.
info auto-load -- Print current status of auto-loaded files.
info auxv -- Display the inferior's auxiliary vector.
info bookmarks -- Status of user-settable bookmarks.
info breakpoints, info b -- Status of specified breakpoints (all user-settable breakpoints if no argument).
info checkpoints -- IDs of currently known checkpoints.
info classes -- All Objective-C classes, or those matching REGEXP.
info common -- Print out the values contained in a Fortran COMMON block.
info connections -- Target connections in use.
--Type <RET> for more, q to quit, c to continue without paging--
```

### Delete breakpoint

- `d <breakpoint number>`：删除指定编号的断点

下面示例删除编号为4的断点：

``` shell
(gdb) info b
Num     Type           Disp Enb Address            What
4       breakpoint     keep y   0x000000000000121e in main at process6.c:19
5       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
(gdb) d 4
(gdb) info b
Num     Type           Disp Enb Address            What
5       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
(gdb) 
```

- `d breakpoints`：删除所有的断点

``` shell
(gdb) info b
Num     Type           Disp Enb Address            What
5       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
6       breakpoint     keep y   0x0000000000001232 in main at process6.c:23
7       breakpoint     keep y   0x0000000000001246 in main at process6.c:25
(gdb) d breakpoints
Delete all breakpoints? (y or n) y
(gdb) info b
No breakpoints or watchpoints.
(gdb) 
```

由上面的示例可以发现，当我们删除某些断点，然后再继续打断点时，**断点的编号会持续上一次的编号继续往下**，这是因为我们没有退出过gdb

### Enable/Disable breakpoints

- `disable b(breakpoints)`：使所有断点无效【默认缺省】

``` shell
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep y   0x000000000000121e in main at process6.c:19
10      breakpoint     keep y   0x000000000000125c in main at process6.c:27
11      breakpoint     keep y   0x00000000000012af in main at process6.c:35
(gdb) disable b
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep n   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep n   0x000000000000121e in main at process6.c:19
10      breakpoint     keep n   0x000000000000125c in main at process6.c:27
11      breakpoint     keep n   0x00000000000012af in main at process6.c:35
(gdb) 
```

可以发现在**Enb**一栏中，状态已经变成了 **n**

- `enable b(breakpoints)`：使所有断点有效【默认缺省】

``` shell
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep n   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep n   0x000000000000121e in main at process6.c:19
10      breakpoint     keep n   0x000000000000125c in main at process6.c:27
11      breakpoint     keep n   0x00000000000012af in main at process6.c:35
(gdb) enable b
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep y   0x000000000000121e in main at process6.c:19
10      breakpoint     keep y   0x000000000000125c in main at process6.c:27
11      breakpoint     keep y   0x00000000000012af in main at process6.c:35
(gdb) 
```

又从**n**变为**y**

- `disable b(breakpoint) <breakpoint number>`：使该编号的断点无效【禁用断点】

``` shell
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep y   0x000000000000121e in main at process6.c:19
10      breakpoint     keep y   0x000000000000125c in main at process6.c:27
11      breakpoint     keep y   0x00000000000012af in main at process6.c:35
(gdb) disable b 9
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep n   0x000000000000121e in main at process6.c:19
10      breakpoint     keep y   0x000000000000125c in main at process6.c:27
11      breakpoint     keep y   0x00000000000012af in main at process6.c:35
(gdb) 
```

可以看到，只有编号为9的断点状态变成了n

- `enable b(breakpoint) <breakpoint number>`：使该编号的断点有效【开启断点】

``` shell
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep n   0x000000000000121e in main at process6.c:19
10      breakpoint     keep y   0x000000000000125c in main at process6.c:27
11      breakpoint     keep y   0x00000000000012af in main at process6.c:35
(gdb) enable b 9
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep y   0x000000000000121e in main at process6.c:19
10      breakpoint     keep y   0x000000000000125c in main at process6.c:27
11      breakpoint     keep y   0x00000000000012af in main at process6.c:35
(gdb) 
```

由**n**变为**y**

### Run

- `r(run)`：若无断点，将直接运行；若有断点，则从第一个断点处开始运行

如果将断点删除掉，使用r指令运行就会直接运行到程序结束：

``` shell
(gdb) disable b --> 禁用所有的断点
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep n   0x00000000000011f5 in main at process6.c:15
9       breakpoint     keep n   0x000000000000121e in main at process6.c:19
10      breakpoint     keep n   0x000000000000125c in main at process6.c:27
11      breakpoint     keep n   0x00000000000012af in main at process6.c:35
(gdb) r
Starting program: /home/xiaowu/linux-review/process/test-debug 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[Detaching after fork from child process 42417]
[Detaching after fork from child process 42418]
[Detaching after fork from child process 42419]
pid:42419 calculate 30000023 is a primer
pid:42418 calculate 30000001 is a primer
pid:42419 calculate 30000041 is a primer
pid:42418 calculate 30000037 is a primer
pid:42419 calculate 30000059 is a primer
pid:42418 calculate 30000049 is a primer
pid:42419 calculate 30000071 is a primer
pid:42418 calculate 30000079 is a primer
pid:42419 calculate 30000083 is a primer
pid:42418 calculate 30000109 is a primer
pid:42419 calculate 30000137 is a primer
pid:42418 calculate 30000133 is a primer
pid:42419 calculate 30000149 is a primer
pid:42418 calculate 30000163 is a primer
pid:42419 calculate 30000167 is a primer
pid:42418 calculate 30000169 is a primer
pid:42418 calculate 30000193 is a primer
pid:42418 calculate 30000199 is a primer
[Inferior 1 (process 42413) exited normally]
(gdb) 
```

**有****断点**的情况：将编号为8的断点启用，程序将运行到第一个**启用的断点**处停下来

``` shell
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep y   0x00005555555551f5 in main at process6.c:15
        breakpoint already hit 1 time
9       breakpoint     keep n   0x000055555555521e in main at process6.c:19
10      breakpoint     keep n   0x000055555555525c in main at process6.c:27
11      breakpoint     keep n   0x00005555555552af in main at process6.c:35
(gdb) r
Starting program: /home/xiaowu/linux-review/process/test-debug 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 8, main () at process6.c:15
15          for (int n = 0; n < N; n++) {
(gdb) 
```

### Step-by-step process

- `n(next)`

逐过程，不会进入函数，在原来的函数中一步一步执行

``` shell
(gdb) r
Starting program: /home/xiaowu/linux-review/process/test-debug 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 8, main () at process6.c:15
15          for (int n = 0; n < N; n++) {
(gdb) b 39
Breakpoint 12 at 0x5555555552c7: file process6.c, line 39.
(gdb) info b
Num     Type           Disp Enb Address            What
8       breakpoint     keep y   0x00005555555551f5 in main at process6.c:15
        breakpoint already hit 1 time
9       breakpoint     keep n   0x000055555555521e in main at process6.c:19
10      breakpoint     keep n   0x000055555555525c in main at process6.c:27
11      breakpoint     keep n   0x00005555555552af in main at process6.c:35
12      breakpoint     keep y   0x00005555555552c7 in main at process6.c:39
(gdb) n
16              pid = fork();
(gdb) n
[Detaching after fork from child process 58753]
17              if (pid < 0) {
(gdb) 
22              if (pid == 0) {
(gdb) 
15          for (int n = 0; n < N; n++) {
(gdb) 
```

### Step

- `s(step)`

逐语句，一次走一条代码，可以进入函数，且库函数也会进入

像这里进入了库函数fork

``` shell
(gdb) 
15          for (int n = 0; n < N; n++) {
(gdb) s
16              pid = fork();
(gdb) s
__libc_fork () at ./posix/fork.c:41
41      ./posix/fork.c: No such file or directory.
(gdb) s
50      in ./posix/fork.c
(gdb) 
```

### Print variable /Trace variable

- `p(print) <variable name>` 打印变量值

``` shell
(gdb) r
Starting program: /home/xiaowu/linux-review/process/test-debug 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 8, main () at process6.c:15
15          for (int n = 0; n < N; n++) {
(gdb) print n
$1 = 1431654656
(gdb) s
16              pid = fork();
(gdb) print n
$2 = 0
(gdb) 
```

运行到15行时，打印n的值是乱码，这是因为此时n只声明未赋值，运行完15行，此时再打印n则正常

继续执行指令n，再回到15行，然后执行完之后，再打印n的值，发现发生了变化

``` shell
(gdb) print n
$2 = 0
(gdb) n
[Detaching after fork from child process 63220]
17              if (pid < 0) {
(gdb) n
22              if (pid == 0) {
(gdb) n
15          for (int n = 0; n < N; n++) {
(gdb) n
16              pid = fork();
(gdb) print n
$3 = 1
(gdb) 
```

这样打印来追踪一个变量比较繁琐，引出下面的方法

- `display <variable name>`：跟踪查看一个变量，每次停下来都会显示它的值【结构体/变量】

``` shell
n
22              if (pid == 0) {
1: n = 1
(gdb) n
15          for (int n = 0; n < N; n++) {
1: n = 1
(gdb) n
16              pid = fork();
1: n = 2
(gdb) 
```

我们也可以**追踪变量的地址**，可以发现**地址不会变化**

``` shell
(gdb) r
Starting program: /home/xiaowu/linux-review/process/test-debug 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 8, main () at process6.c:15
15          for (int n = 0; n < N; n++) {
1: n = 1431654656
2: &n = (int *) 0x7fffffffd708
(gdb) n
16              pid = fork();
1: n = 0
2: &n = (int *) 0x7fffffffd708
(gdb) n
[Detaching after fork from child process 66025]
17              if (pid < 0) {
1: n = 0
2: &n = (int *) 0x7fffffffd708
(gdb) n
22              if (pid == 0) {
1: n = 0
2: &n = (int *) 0x7fffffffd708
(gdb) n
15          for (int n = 0; n < N; n++) {
1: n = 0
2: &n = (int *) 0x7fffffffd708
(gdb) n
16              pid = fork();
1: n = 1
2: &n = (int *) 0x7fffffffd708
(gdb) 
```

- `undisplay <variable number>`：取消对先前设置的变量的追踪，注意，这里要的是变量名编号

这里使用undisplay取消了对编号为1的变量的追踪

``` shell
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
1:   y  n
2:   y  &n
(gdb) undisplay 1
(gdb) info display
Auto-display expressions now in effect:
Num Enb Expression
2:   y  &n
(gdb) 
```

### Print register value

- `p $a0` 这里就是打印出a0寄存器的值

### Print values in hexadecimal format

- `p/x 1536` 以16进制格式打印1536

### Check register info

- `i r a0` 查看寄存器a0的信息

### Check function call

- `bt`：看到底层函数的调用过程【**函数压栈**】

像这里我们是从main函数进入了wait函数，那么使用bt指令，打印出的顺序而是先wait再main，**类似与压栈的过程**

``` shell
(gdb) s
__wait (stat_loc=0x0) at ./posix/wait.c:24
24      ./posix/wait.c: No such file or directory.
(gdb) bt
#0  __wait (stat_loc=0x0) at ./posix/wait.c:24
#1  0x00005555555552da in main () at process6.c:40
(gdb) 
```

### Modify the value of variable

- `set var` ：修改变量的值

这里执行到n为1，此时我们使用指令修改n的值为0，然后再使用n就可以打印出n的值

``` shell
(gdb) 
16              pid = fork();
2: &n = (int *) 0x7fffffffd708
4: n = 1
(gdb) set var n = 0
(gdb) n
[Detaching after fork from child process 77087]
17              if (pid < 0) {
2: &n = (int *) 0x7fffffffd708
4: n = 0
(gdb) 
```

### Specify the line number to jump (in common use)

- `until <line number>`：进行指定位置跳转，执行完区间代码

可以直接结束一些循环，到达你想要执行的位置

这里从17行直接跳到37行，但是37行没有代码，所以执行到下面有代码的第39行

``` shell
[Detaching after fork from child process 77087]
17              if (pid < 0) {
2: &n = (int *) 0x7fffffffd708
4: n = 0
(gdb) until 37
[Detaching after fork from child process 80080]
[Detaching after fork from child process 80081]

Breakpoint 12, main () at process6.c:39
39          for (n = 0; n < N; n++) {
(gdb) pid:80080 calculate 30000001 is a primer
pid:80081 calculate 30000023 is a primer
pid:80080 calculate 30000037 is a primer
pid:80081 calculate 30000041 is a primer
pid:80080 calculate 30000049 is a primer
pid:80081 calculate 30000059 is a primer
pid:80080 calculate 30000079 is a primer
pid:80081 calculate 30000071 is a primer
pid:80080 calculate 30000109 is a primer
pid:80081 calculate 30000083 is a primer
pid:80080 calculate 30000133 is a primer
pid:80081 calculate 30000137 is a primer
pid:80080 calculate 30000163 is a primer
pid:80081 calculate 30000149 is a primer
pid:80080 calculate 30000169 is a primer
pid:80081 calculate 30000167 is a primer
pid:80080 calculate 30000193 is a primer
pid:80080 calculate 30000199 is a primer
```

### Enforce function

- `finish`：在一个函数内部，执行到当前函数返回，然后停下来等待命令

我们可以使用【finsh】指令来直接使一个函数执行完毕

这里先是s指令单步执行进入wait函数，然后使用finish指令将wait函数执行完毕，返回到39行

``` shell
s
40              wait(NULL);
(gdb) s
__wait (stat_loc=0x0) at ./posix/wait.c:24
24      ./posix/wait.c: No such file or directory.
(gdb) finish
Run till exit from #0  __wait (stat_loc=0x0) at ./posix/wait.c:24
main () at process6.c:39
39          for (n = 0; n < N; n++) {
Value returned is $4 = 76960
(gdb) 
```

### Jump to the next breakpoint

- `c(continue)`：从一个断点处，直接运行到下一个断点处

这里在第15行和39行打上断点，然后运行r指令到达第一个断点，再运行c指令到达第二个断点

``` shell
(gdb) b 15
Breakpoint 1 at 0x11f5: file process6.c, line 15.
(gdb) b 39
Breakpoint 2 at 0x12c7: file process6.c, line 39.
(gdb) info b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00000000000011f5 in main at process6.c:15
2       breakpoint     keep y   0x00000000000012c7 in main at process6.c:39
(gdb) r
Starting program: /home/xiaowu/linux-review/process/test-debug 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, main () at process6.c:15
15          for (int n = 0; n < N; n++) {
(gdb) c
Continuing.
[Detaching after fork from child process 1473]
[Detaching after fork from child process 1474]
[Detaching after fork from child process 1475]

Breakpoint 2, main () at process6.c:39
39          for (n = 0; n < N; n++) {
(gdb) pid:1475 calculate 30000023 is a primer
pid:1474 calculate 30000001 is a primer
pid:1475 calculate 30000041 is a primer
pid:1474 calculate 30000037 is a primer
pid:1475 calculate 30000059 is a primer
pid:1474 calculate 30000049 is a primer
pid:1475 calculate 30000071 is a primer
pid:1474 calculate 30000079 is a primer
pid:1475 calculate 30000083 is a primer
pid:1474 calculate 30000109 is a primer
pid:1475 calculate 30000137 is a primer
pid:1474 calculate 30000133 is a primer
pid:1475 calculate 30000149 is a primer
pid:1474 calculate 30000163 is a primer
pid:1475 calculate 30000167 is a primer
pid:1474 calculate 30000169 is a primer
pid:1474 calculate 30000193 is a primer
pid:1474 calculate 30000199 is a primer
n
40              wait(NULL);
(gdb) 
```