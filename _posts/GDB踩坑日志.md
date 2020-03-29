---
title: GDB踩坑日志
date: 2019-10-1 13:06:30
tags: 
	- linux
categories:
	- 学习
---

# GDB踩坑日志

gdb这玩意真是坑，因为之前做信息安全技术的实验，导致ubuntu系统崩了一次，然后整个gdb就不正常了。**无法再调试32bit**的程序，重新sudo apt-get install gdb也不行，具体表现如下：

![gdb](/picture/gdb.png)

**之后选择源码安装：**

## 源码安装及报错

在这个[网站](http://ftp.gnu.org/gnu/gdb/)上下载一个.gz文件。

我是把它下载在~/GDB文件夹下的；

然后

```bash
tar -zxvf gdb-8.3.tar.gz 
```

建一个文件夹：GDB_build

```bash
mkdir GDB_build
```

之后进入该文件夹，然后

```bash
../gdb-8.3/configure
```

然后,make编译的时间有点长

```
make -j4
```

最后：

```
sudo make install
```

本以为一切顺利，没想到！还是有错：

![1569651547335](/picture/1569651547335.png)

## 修复

上面错误先忽略一下，然后bash下输入:

```bash
gdb --version
```

出现了如下错误信息：

```bash
Python Exception <type 'exceptions.ImportError'> No module named gdb: 
gdb: warning: 
Could not load the Python gdb module from `/usr/local/share/gdb/python'.
Limited Python support is available from the _gdb module.
Suggest passing --data-directory=/path/to/gdb/data-directory.


```

我就很绝望了，到底什么鬼?而且，peda插件也运行不了，就准备重装系统了，最后在stackoverflow上看到一人[回答](https://stackoverflow.com/questions/25597445/python-exception-type-exceptions-importerror-no-module-named-gdb)，还是非常有效的。

解决方法如下：

把解压后的gdb源码，路径应该在~/GDB/gdb-8.3/gdb/python/lib/gdb/下，复制一份到  /usr/local/share/gdb/python/gdb/下；

```bash
sudo mkdir -p /usr/local/share/gdb/python/gdb
sudo cp -rf ~/GDB/gdb-8.3/gdb/python/lib/gdb/* /usr/local/share/gdb/python/gdb/
```

之后，gdb就可以用了

![1569651918368](/picture/1569651918368.png)

而且，后面装peda插件也行了！详细peda教程见[这篇](https://github.com/longld/peda)。而且最后，32bit程序也可以调试了！！！



