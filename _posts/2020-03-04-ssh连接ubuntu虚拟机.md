---
title: ssh连接Ubuntu虚拟机
tags:
 - Linux
---

# ssh连接Ubuntu虚拟机

说明，因为在windows装了一个ubuntu的虚拟机（server版本的），不含图形界面。操作起来比较麻烦，比如不能从windows外面复制一些命令再输入到命令行下等等，也是不想在虚拟机与windows之间切来切去，比较费时，所以就在windows下利用ssh连接一下ubuntu虚拟机。



## 系统说明

- Windows10
- VMware下的ubuntu虚拟机:ubuntu 18.04.3 LTS

## 一、Ubuntu虚拟机开启ssh服务

bash下输入：

```bash
sudo apt-get install openssh-server
```

启动ssh server

```
sudo ps -e |grep ssh
```

如果出现了类似这样的信息，就说明启动ssh服务成功了。

![image-20200304192944821](/assets/image/image-20200304192944821.png)

如果没有，输入：

```bash
sudo service ssh restart
```

## 二、虚拟机和windows之间网络互通

在进行下一步之前，需要检查虚拟机和windows是否可以ping通。

windows的cmd下输入ipconfig语句，可查得自己的ip地址；

linux下输入ifconfig可以获得虚拟机的ip地址；

然后互相利用ping命令ping对方的地址。

比如windows下：192.168.2.109是虚拟机的ip地址；

![image-20200304193437523](/assets/image/image-20200304193437523.png)

linux下：后面为windows主机的ip地址；

![image-20200304193613557](/assets/image/image-20200304193613557.png)

在这里可能会遇到windows可以ping通linux，而linux却ping不通windows的情况。

解决办法如下：

### 更改虚拟机的配置

这里需要设置成桥接模式

![image-20200304194346968](/assets/image/image-20200304194346968.png)

### Windows防火墙的设置

参考这一篇[博客](https://blog.csdn.net/qq_26822029/article/details/80428138)，把防火墙设置一下：找到文件和打印机共享

![image-20200304194657021](/assets/image/image-20200304194657021.png)

## 三、Windows下使用ssh命令连接虚拟机

因为，linux下的openssh server服务进程默认端口号是22，所以在windows的cmd下输入：

```bash
ssh -p 22 your_ubuntu_name@ip
```

![image-20200304195238088](/assets/image/image-20200304195238088.png)

至此，大工告成。



## 参考

https://blog.csdn.net/qq_26822029/article/details/80428138 ，2019-3-4