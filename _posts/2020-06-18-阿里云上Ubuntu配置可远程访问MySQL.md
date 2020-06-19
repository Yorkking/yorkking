---
title: 阿里云Ubuntu下配置可远程访问MySQL
tags:
 - MySQL
---

## 安装 MySQL

首先，是确认自己的 ubuntu 的版本：

```bash
 cat /proc/version
```

![image-20200618232451086](/assets/image/image-20200618232451086.png)

然后去[官网](https://dev.mysql.com/downloads/mysql/)根据自己版本下载相应版本（注，一定要选对版本），我选择的是Ubuntu 16.04版本对应的MySQL。

![image-20200618232723809](/assets/image/image-20200618232723809.png)

然后可以使用 wget 命令下载至目录下：

```bash
cd ~ && mkdir mysql && cd mysql
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-server_8.0.20-1ubuntu16.04_amd64.deb-bundle.tar
```

 然后解压：

```bash
tar -xvf mysql-server_8.0.20-1ubuntu16.04_amd64.deb-bundle.tar
```

所要安装的文件如下：

![image-20200618232824348](/assets/image/image-20200618232824348.png)

然后安装的顺序为，参考[这个](https://blog.csdn.net/hu_feng903/article/details/80298151)，安装过程可能会有错，仔细阅读它的出错提示，按要求继续。

总之就是先安装common，然后 client，最后server。

```bash
dpkg -i mysql-common_5.7.20-1ubuntu14.04_amd64.deb
dpkg -i mysql-community-client_5.7.20-1ubuntu14.04_amd64.deb
dpkg -i mysql-client_5.7.20-1ubuntu14.04_amd64.deb
apt-get install libmecab2
dpkg -i mysql-community-server_5.7.20-1ubuntu14.04_amd64.deb
```

安装完成可以通过 `ps -aux | grep mysql` 查看是否正常运行 MySQL 。

## 开启远程访问服务

首先，进入 MySQL 并进入 `mysql` 数据库

```bash
mysql -u root -p
use mysql;
```

查询一下当前的用户和开放情况：

```bash
select host,user from mysql.user;
```

![image-20200618233109176](/assets/image/image-20200618233109176.png)

然后设置一个对外开发的用户：（以下命令对应MySQL版本为Server version: 8.0.20 MySQL Community Server - GPL）% 表示对所有主机均可访问。

```
CREATE USER '用户名'@'%' IDENTIFIED BY '密码'; 
GRANT ALL ON *.* TO '用户名'@'%'; 
ALTER USER '用户名'@'%' IDENTIFIED WITH mysql_native_password BY '密码';
FLUSH PRIVILEGES;
```

然后重新开启MySQL的服务：

```bash
service mysql restart
```

然后看一下是否开启远程服务：MySQL默认端口为3306

```bash
netstat -an |grep 3306
```

![image-20200618231937631](/assets/image/image-20200618231937631.png)

红框部分如图所示，表示是对外开放，所有IP地址均可访问。

### [最后一步](https://www.cnblogs.com/songyao666/p/11138313.html)

这一步是在阿里云上需要操作的，需要把 3306 这个端口暴露出去，运行访问。

也是最重要的一步，阿里云的服务器设置了安全组规则来限制ecs服务器的ip,端口访问策略。因此需要修改。

登录阿里云=>控制台=>云服务器ECS=>网络和安全=>安全组

在入方向，点击配置规则可以看到下图，3306端口是访问服务器mysql的，没有的话就添加规则，端口范围选择 3306mysql,授权对象设置为0.0.0.0/0 允许所有ip访问。

![image-20200618232227013](/assets/image/image-20200618232227013.png)

最终就可以进行在本地远程访问数据库了：

```bash
 mysql -h 主机公网IP -P 3306 -u root -p
```

![image-20200618232350769](/assets/image/image-20200618232350769.png)

大功告成！