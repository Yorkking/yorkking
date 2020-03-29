---
title:  Ubuntu系统中文输入法崩溃修复问题
data: 2019-8-24 13:53
tags: 
	- linux
categories:
	- 学习
---

# Ubuntu系统中文输入法崩溃修复问题

近日一直在折腾ubuntu，可是折腾折腾，把原本的中文输入法搞崩了。对于新手而言，这绝对是异常痛苦的。

于是上网查各种教程，却都无法解决问题。最后，干脆果断放弃iBus，于是改用现在的搜狗输入法了。

参考这一篇[教程](https://www.cnblogs.com/cursorhu/p/6564793.html)。

## Sogou输入法的安装流程

### 1.下载Sogou输入法

去官网，下载搜狗输入法for Linux: https://pinyin.sogou.com/linux/

### 2. 安装fcitx框架

搜狗输入法是基于fcitx框架的，所以在安装搜狗输入法之前，得安装这个框架。

- 添加源，bush下输入：

  ```bush
  sudo add-apt-repository ppa:fcitx-team/nightly
  sudo apt-get update
  ```

- 安装fcitx：

  ```bush
  sudo apt-get install fcitx
  sudo apt-get install fcitx-config-gtk
  sudo apt-get install fcitx-table-all
  ```

- 安装im-switch：

  ```bush
  sudo apt-get install im-switch
  ```

- 安装搜狗输入法

  切换到刚刚下载搜狗输入法安装包那个目录下，在bush下输入：

  ```
  sudo dpkg -i  sogoupinyin_2.2.0.0102_amd64.deb # 后面是下载的搜狗输入法安装包名字
  ```

然后重启系统。

> ### *2-1 可能的附加步骤
>
> 如果进行完后面的步骤3依然无法输入中文，或步骤三无法正常进行，执行该步骤：
>
> 在~/.bashrc中加入以下几行：（用vim打开编辑）
>
> ```bush
> export GTK_IM_MODULE=fcitx      
> export QT_IM_MODULE=fcitx      
> export XMODIFIERS="@im=fcitx"  
> ```
>
> 之后，终端输入：
>
> ```bash
> source ~/.bashrc
> ```
>
> 



### 3. 设置搜狗输入法

打开语言设置：

![1566625379532](/picture/1566625379532.png)

然后把这里设置成fcitx。



然后是输入法设置，一般桌面右上角：

之后，设置成搜狗输入法，然后就可以有中文输入法了。

![1566625760469](/picture/1566625760469.png)





## 参考

[1]https://www.cnblogs.com/voyagee/p/6898054.html, 2019-8-13

[2] https://www.cnblogs.com/voyagee/p/6898054.html, 2019-8-13

