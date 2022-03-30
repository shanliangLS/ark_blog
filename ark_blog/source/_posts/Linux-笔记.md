---
title: Linux 笔记
date: 2021-01-19 16:48:21
categories:
- 快速上手
- 操作系统
tags:
- Linux
---

Linux 软件安装、操作笔记

<!-- more -->

## 用户

### 创建新用户

1. 添加用户

   ```bash
   adduser liangsh
   ```

   修改密码

   ```bash
   passwd liangsh
   ```

   输入两次新密码

2. 赋予root权限（可以使用sudo）

   修改 `/etc/sudoers` 文件：

   ```
   ## Allow root to run any commands anywhere
   root    ALL=(ALL)     ALL
   liangsh   ALL=(ALL)     ALL
   ```

## 安装 jdk

查询 yum 下所有的 java 版本

```bash
yum list java*
```

安装 openjdk1.8

```bash
yum install java-1.8.0-openjdk.x86_64
```

配置 java 环境

```bash
vim etc/profile
```

在最底下添加

```bash
#set java environment
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64
JRE_HOME=$JAVA_HOME/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```

检测是否安装成功

```bash
java -version
```

## 安装 redis

### 编译安装

进入官网找到下载地址 https://redis.io/download

解压

```bash
tar -zvxf redis-5.0.7.tar.gz
```

一般将 redis 目录放置到 `/usr/local/redis` 目录

```bash
mv ./redis-5.0.7 /usr/local/redis
```

cd 到 `/usr/local/redis` 目录，输入命令 `make` 执行编译命令

```bash
make
```

安装

```bash
make PREFIX=/usr/local/redis install
```

> 这里多了一个关键字 `PREFIX=` 这个关键字的作用是编译的时候用于指定程序存放的路径。比如我们现在就是指定了 redis 必须存放在 `/usr/local/redis` 目录。假设不添加该关键字 Linux 会将可执行文件存放在 `/usr/local/bin` 目录，库文件会存放在 `/usr/local/lib` 目录。配置文件会存放在 `/usr/local/etc` 目录。其他的资源文件会存放在 `usr/local/share` 目录。这里指定号目录也方便后续的卸载，后续直接 `rm -rf /usr/local/redis` 即可删除 redis

### rpm安装

访问 http://pkgs.org/download/redis 根据 linux 平台以及版本下载对应的 rpm 包

安装

```bash
rpm -ivh redis-3.0.7-4.el7.art.x86_64.rpm
```

### 配置

配置 redis 为后台启动

```bash
vi /usr/local/redis/redis.conf
```

将 `daemonize no` 改成 `daemonize yes`

将 redis 加入到开机启动

```bash
vi /etc/rc.local
```

在里面添加内容：

```bash
/usr/local/redis/bin/redis-server /usr/local/redis/redis.conf
```

在目录 `/usr/local/redis` 启动 redis

```bash
./bin/redis-server ./redis.conf
```

## 安装 tomcat

下载 [Apache tomcat](https://tomcat.apache.org/download-80.cgi)

解压

```bash
tar -zxvf apache-tomcat-8.5.37.tar.gz  
```

移动到 `/usr/local/tomcat`

运行  `/usr/local/tomcat/bin/startup.sh`  启动 tomcat，默认端口8080

运行` /usr/local/tomcat/bin/shutdown.sh` 关闭 tomcat

### 配置

进入 `/usr/local/tomcat/conf` 目录修改 `server.xml` 中的 tomcat 端口，用 `/8080` 定位到8080端口

启动失败解决办法

如果不能访问，说明没有开启8080端口

先查看防火墙开放端口列表 `firewall-cmd --zone=public --list-ports`

如果没有8080端口，添加端口

-  如果是 firewalld 防火墙 ，开启防火墙端口 `firewall-cmd --zone=public --add-port=8080/tcp --permanent` 并且重新加载防火墙 `firewall-cmd --reload`
-  如果是 iptables 防火墙 ，执行 `vi /etc/sysconfig/iptables` 加入下面内容 `-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT`

## 安装 mysql

博客参考 https://www.cnblogs.com/wendy-0901/p/12673705.html

### yum 安装

```bash
yum install mysql
```

启动

```bash
service mysqld start
```

第一次进入 mysql 不需要密码

```bash
mysql -u root -p
```

选择数据库

```bash
mysql>use mysql;
```

修改密码

```bash
mysql>UPDATE user SET password=PASSWORD('123456') WHERE user='root';
```

刷新

```bash
mysql>flush privileges
```

### 用命令执行 sql 文件

新建数据库

```bash
mysql>create database 新建数据库;
```

进入该数据库

```bash
mysql>use 新建数据库
```

导入文件，使用绝对路径

```bash
mysql>source sql文件
```

## 安装 glibc_2.18 和 libstdc++.so

参考 [Linux下安装GLIBC_2.18和libstdc++.so](https://www.jianshu.com/p/f23129adb8c4/)

Tips：如果不装libstdc++.so会报错：`ImportError: /usr/lib64/libstdc++.so.6: version 'GLIBCXX_3.4.15' not found (required by /opt/calibre/lib/libQt5WebKit.so.5)`

- `GLIBC_2.18`,`GLIBCXX_3.4.19`

```
# yum install libstdc++.so.6 -y
--- 查看动态库版本有哪些
# strings /usr/lib64/libstdc++.so.6 | grep GLIBC
# wget http://ftp.de.debian.org/debian/pool/main/g/gcc-4.8/libstdc++6-4.8-dbg_4.8.4-1_amd64.deb
# ar -x libstdc++6-4.8-dbg_4.8.4-1_amd64.deb
# tar -xvf data.tar.xz
-- 拷贝至/usr/lib64下
# cp ./usr/lib/x86_64-linux-gnu/debug/libstdc++.so.6.0.19 /usr/lib64
-- 授权
# chmod +x /usr/lib64/libstdc++.so.6.0.19
-- 删除低版本库的软连接
# rm /usr/lib64/libstdc++.so.6
# ln -s /usr/lib64/libstdc++.so.6.0.19 /usr/lib64/libstdc++.so.6

# cd /usr/local
# wget http://mirrors.ustc.edu.cn/gnu/libc/glibc-2.18.tar.gz
# tar -xzvf glibc-2.18.tar.gz
# cd glibc-2.18
# mkdir build && cd build/
# ../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
# make -j4
# make install
--- 解决中文乱码问题
# make localedata/install-locales
--- 检查验证
# ll /lib64/libc*
--- 查看系统glibc支持的版本
# strings /lib64/libc.so.6 |grep GLIBC
# /lib64/libc.so.6
```

## Docker

安装

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce
```

启动

```bash
service docker start
```

### 下载镜像到本地

```bash
docker pull <repository>:<tag> # 必须先pull镜像到本地
docker save <repository>:<tag> -o <repository>.tar # 保存为tar包
```

直接使用 IMAGE ID 保存，导入后 repository 和 tag 名称会显示 none

```bash
docker save <IMAGE ID> -o <repository>.tar # 会导致载入镜像后名字标签都为<none>
```

如果 docker 载入新的镜像后 repository 和 tag 都为 none，那么通过 tag 的方法增加名字标签

```bash
docker tag <IMAGE ID> <repository>:<tag>
```

### 载入镜像

```bash
docker load --input <repository>.tar
```

### 删除镜像

```bash
docker rmi -f <IMAGE ID>
```

`-f` 表示强制删除镜像