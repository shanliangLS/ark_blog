---
title: Docker 安装与入门
date: 2022-10-09 08:54:00
categories:
- 快速上手
tags:
- Docker
- Linux
---

Docker 软件安装、常用命令

<!-- more -->

## 安装

### 在线安装

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce
```

启动

```bash
service docker start
service docker status
```

### 离线安装

下载安装包，可访问网址查看最新版本

```bash
# docker安装包下载
https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-18.09.8-3.el7.x86_64.rpm

# 依赖包下载
# containerd.io
https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.2-3.el7.x86_64.rpm
# docker-ce-cli
https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-cli-18.09.8-3.el7.x86_64.rpm
# container-selinux
https://pkgs.org/download/container-selinux
```

安装

```bash
rpm -Uvh --force --nodeps *.rpm
```

启动

```bash
service docker start
service docker status
```

#### docker-compose

下载，可访问网址查看最新版本  
`$(uname -s)` 即系统名称（Linux），`(uname -m)` 即系统架构（x86_64）

```bash
# 这个是官方地址，可能比较慢，推荐使用下面的国内镜像地址
curl -L "https://github.com/docker/compose/releases/download/2.11.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 国内镜像地址
curl -L https://get.daocloud.io/docker/compose/releases/download/2.11.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

# 下载完之后可以看下 /usr/local/bin 这个目录有没有 docker-compose 这个文件
```

授权

```bash
# 给docker compose 目录授权
sudo chmod +x /usr/local/bin/docker-compose

# 查看一下version，显示有版本号那就说明安装成功了
docker-compose version
```

### 启动异常

#### OCI runtime create failed

启动镜像时报

```bash
docker: Error response from daemon: failed to create shim: OCI runtime create failed: unable to retrieve OCI runtime error (open /run/containerd/io.containerd.runtime.v2.task/moby/cdf3729f103e2f4cb94e25f3cfe6c21a3126428c0a977b7651ed5a77ab6d7/log.json: no such file or directory): runc did not terminate successfully: unknown.
```

下载 [runc](https://github.com/opencontainers/runc/tags) 替换

```bash
cp runc.amd64 /usr/local/bin/runc
cp runc.amd64 /usr/local/sbin/runc
cp runc.amd64 /usr/bin/runc
chmod +x /usr/local/bin/runc
chmod +x /usr/local/sbin/runc
chmod +x /usr/bin/runc
```

## 镜像

### 下载镜像到本地

```bash
docker pull <repository>:<tag> # 必须先pull镜像到本地
docker save <repository>:<tag> -o <repository>.tar # 保存为tar包
```

### 导出镜像

```bash
docker save [options] images [images...]
```

如

```bash
docker save mongo:latest -o mongo.tar
或 
docker save mongo:latest > mongo.tar
```

`-o` 和 `>` 表示输出到文件，`nginx:latest` 是源镜像名（name:tag）

导出多个镜像到一个tar包

```bash
docker save $(docker images | grep -v REPOSITORY | awk 'BEGIN{OFS=":";ORS=" "}{print $1,$2}') -o kube.tar
```

直接使用 IMAGE ID 保存，导入后 repository 和 tag 名称会显示 none

```bash
docker save <IMAGE ID> -o <repository>.tar # 会导致载入镜像后名字标签都为<none>
```

如果 docker 载入新的镜像后 repository 和 tag 都为 none，那么通过 tag 的方法增加名字标签

```bash
docker tag <IMAGE ID> <repository>:<tag>
```

### 导入镜像

```bash
docker load [options]
```

如

```bsah
docker load -i mongo.tar
或
docker load < mongo.tar
```

`-i` 和 `<` 表示从文件输入。会成功导入镜像及相关元数据，包括 tag 信息

### 删除镜像

```bash
docker rmi -f <IMAGE ID>
```

`-f` 表示强制删除镜像

推荐先删除引用该镜像的容器，再不使用 `-f` 删除镜像

## 容器
