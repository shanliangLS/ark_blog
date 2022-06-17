---
title: CentOS7 升级 cmake
date: 2022-06-02 ‏‎10:05:00
categories:
- 快速上手
- 操作系统
tags:
- Linux
- cmake
---

CMake 3.0.0 or higher is required. You are running version 2.8.12.2

<!-- more -->

参考 [centos7升级 cmake](https://blog.csdn.net/weixin_45661908/article/details/124076857)

使用安装命令无法升级 cmake

```bash
yum remove -y cmake
sudo yum -y install cmake
```

仍是 2.8.12.2

```bash
[root@localhost build]# cmake -version
cmake version 2.8.12.2
```

只能编译安装

## 删除旧版本 cmake

```bash
yum remove -y cmake
```

## 安装需要的模块

```bash
yum install -y libxml2 libxml2-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel zstd libzstd-devel curl libcurl-devel libpng libpng-devel
```

## 下载 cmake 源代码

官网地址为 [https://cmake.org](https://cmake.org)，选择 download 中的源代码文件

```bash
wget https://github.com/Kitware/CMake/releases/download/v3.23.0/cmake-3.23.0.tar.gz
tar -zxvf cmake-3.23.0.tar.gz
```

## 编译 cmake

```bash
cd cmake-3.23.0
mkdir build
cd build
../configure --prefix=/usr/local/cmake-3.23.0
gmake -j2 # 2核服务器配置的参数
# make install
gmake install
```

## 配置环境

```bash
#设置环境变量
touch /etc/profile.d/cmake.sh
chmod 777 /etc/profile.d/cmake.sh 
echo -e '\nexport PATH=/usr/local/cmake-3.23.0/bin:$PATH\n' >> /etc/profile.d/cmake.sh
source /etc/profile.d/cmake.sh
 
#设置头文件
# ln -sv /usr/local/gcc-11.2.0/include/c++/11.2.0 /usr/include/c++/11.2.0
 
#设置库文件
# touch /etc/ld.so.conf.d/gcc.conf
# chmod 777 /etc/ld.so.conf.d/gcc.conf 
# echo -e "/usr/local/gcc-11.2.0/lib64" >> /etc/ld.so.conf.d/gcc.conf
 
#加载动态连接库
# ldconfig -v
# ldconfig -p |grep gcc
```

重启服务器

## 查看cmake版本

```bash
[root@localhost build]# cmake -version
cmake version 3.23.0

CMake suite maintained and supported by Kitware (kitware.com/cmake).
```

cmake 升级成功
