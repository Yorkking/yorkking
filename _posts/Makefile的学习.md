---
title: Makefile的学习
date: 2019-10-1 13:06:30
tags: linux

---



# Makefile的学习

## 1. 为什么要用makefile

在编译多个文件时，比如：

![1566632689000](../picture/1566632689000.png)

需要在命令行下输入：

```bash
gcc -o hellomake hellomake.c hellofunc.c -I.
```

这样有两个缺点：

> - 需要多次尝试编译程序时，需要在命令行下重复输入上述语句。当然，shell下可以通过按`上箭头`来复现，但是万一shell关闭，上述命令丢失时；或者在另外一台计算机上编译相应的程序时，就需要重新输入一遍了，这样效率低。
> - 当只更改了其中一个文件时，只需要重新编译相关联的部分文件时，进行上述编译操作，会使得所有文件都重新编译生成。这样会浪费时间。



Makefile可以克服上述两个缺点。



## 2. Makefile的语法

Makefile 1初版

```makefile
hellomake: hellomake.c hellofunc.c
	gcc -o hellomake hellomake.c hellofunc.c -I.
```

这样只解决了第一个缺点。

Makefile 2 改进版

```makefile
CC = gcc
CFLAGS = -I.

hellomake: hellomake.o hellofunc.o
	$(CC) -o hellomake hellomake.o hellofunc.o
```

这样写也可以，make会默认把文件编译成.o文件。

- gcc那一行，需要有一个tab

- `-o $@`: 生成output文件的文件名为`：`左边的文件名。
- `$<`是dependencies list中的第一项。
- `$@` 指的是`:`的左边

- `$^`指的是`:`的右边

  

## 参考

[1] http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/, 2019-8-24