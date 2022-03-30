---
title: Linux 安装 GLIBC_2.18 和 libstdc++.so
date: 2022-01-15 ‏‎12:00:00
categories:
- 快速上手
- 操作系统
tags:
- Linux
---

参考 [Linux下安装GLIBC_2.18和libstdc++.so](https://www.jianshu.com/p/f23129adb8c4)

Tips: 如果不装 libstdc++.so 会报错：`ImportError: /usr/lib64/libstdc++.so.6: version 'GLIBCXX_3.4.15' not found (required by /opt/calibre/lib/libQt5WebKit.so.5)`

- `GLIBC_2.18`
- `GLIBCXX_3.4.19`

```shell
yum install libstdc++.so.6 -y
# 查看动态库版本有哪些
strings /usr/lib64/libstdc++.so.6 | grep GLIBC
wget http://ftp.de.debian.org/debian/pool/main/g/gcc-4.8/libstdc++6-4.8-dbg_4.8.4-1_amd64.deb
ar -x libstdc++6-4.8-dbg_4.8.4-1_amd64.deb
tar -xvf data.tar.xz
# 拷贝至/usr/lib64下
cp ./usr/lib/x86_64-linux-gnu/debug/libstdc++.so.6.0.19 /usr/lib64
# 授权
chmod +x /usr/lib64/libstdc++.so.6.0.19
# 删除低版本库的软连接
rm /usr/lib64/libstdc++.so.6
ln -s /usr/lib64/libstdc++.so.6.0.19 /usr/lib64/libstdc++.so.6
cd /usr/local
wget http://mirrors.ustc.edu.cn/gnu/libc/glibc-2.18.tar.gz
tar -xzvf glibc-2.18.tar.gz
cd glibc-2.18
mkdir build && cd build/
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
make -j4
make install
# 解决中文乱码问题
make localedata/install-locales
# 检查验证
ll /lib64/libc*
# 查看系统glibc支持的版本
strings /lib64/libc.so.6 |grep GLIBC
/lib64/libc.so.6
```