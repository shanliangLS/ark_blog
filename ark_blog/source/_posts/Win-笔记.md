---
title: Win 笔记
date: 2022-03-30 11:37:00
categories:
- 快速上手
- 操作系统
tags:
- Windows
---

## 查看 win10 下某端口被什么进程占用以及 kill

根据端口查看 pid

```cmd
netstat -aon | findstr "端口号"
```

根据 pid 查看进程信息

```cmd
tasklist | findstr "进程号"
```

杀掉

```cmd
taskkill -f -pid "进程号"
```
