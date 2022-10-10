---
title: Linux 防火墙常用命令
date: 2022-10-10 09:32:00
categories:
- 快速上手
- 操作系统
tags:
- Linux
---

## 查看端口

```bash
firewall-cmd --list-ports
```

## 开放端口

```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
```

## 关闭端口

```bsah
firewall-cmd --zone=public --remove-port=80/tcp --permanent
firewall-cmd --reload
```

## 禁止防火墙开机启动

```bash
systemctl disable firewalld.service
```
