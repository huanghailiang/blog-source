---
title: CUDA学习笔记（二）之调试工具:cuda-gdb
date: 2018-07-02 10:30:21
tags: CUDA
---

# 一、Linux下命令行操作

## 使用cuda-gdb对CUDA程序进行编译
<!--more-->
## 1、编译程序

编译程序的时候我们使用nvcc编译器，将其编译成可调式版本；-g 表示将CPU代码(host)编译成可调式版本，-G表示将GPU代码(kernel)编译成可调式版本。

例子：
```bash
nvcc -g -G XXX.cu -o XXX
```

## 2、进入调试器

```bash
cuda-gdb
```
进入调试器后可以使用3中的指令进行执行可执行文件，然后进行调试，也可以在cuda-gdb指令后面直接加上需要调试的可执行文件进行调试。

## 3、调试的基本指令


指令|简写|指令说明|举例|
---|---|---|---
file exe_name| |置顶可调试的可执行文件|file program
set args arg1 arg2 …| |设置命令行参数|set args 1 2
breakpoint|b|设置断点，使代码在指定位置暂停执行。其参数可以是方法名，也可以是行号|b main; b 数字
run|r	|在调试器中执行程序	|
start|		|开始执行程序，并在main的第一行停住|	
next|	n	|单步执行到下一行	|
continue|c|继续执行已暂停的程序至下一个断点或程序结尾处|
step|	s	|单步执行，会进入函数内部执行	|
continue|	c	|执行已暂停程序到下一断点或结尾处	|
print|	p	|打印参数信息,查看变量	|p var1
thread|		|列出当前主机线程	|
cuda|		|列出当前活跃的kernel/grid/block/thread内容，并允许将焦点移至此处	|cuda thread(1, 1, 1); cuda kernel 1 block(1, 2, 1)
cuda thread||列出当前活跃的GPU线程（若有的话）|
cuda kernel||列出当前活跃的GPU Kernel，并允许将“焦点”转移到指定的GPU线程|
info|		|查看参数所包含的具体信息|	info devices; info kernels; info threads
backtrace|	bt	|显示当前函数调用栈的内容|
quit|q|退出cuda-gdb|

# 二、Linux下使用图形界面

当我们装好了CUDA的时候，其实在图形界面下已经装好了一个叫“nsight”的编译器，我们可以直接用终端打开这个编译器，然后写好程序直接编译然后debug就可以了。



# 参考
https://blog.csdn.net/sinat_28750977/article/details/69062708
http://book.51cto.com/art/201301/376309.htm
