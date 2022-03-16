---
title: npm install
date: 2022-03-16 14:05:00
categories:
- 前端
tags:
- npm
---

## node-sass

参考[整理 node-sass 安装失败的原因及解决办法(小结)](https://www.jb51.net/article/180781.htm)

如果 package.json 里有 node-sass， npm install 时经常遇到报错：没有安装 python 或 node-sass 安装失败的问题，百度之后发现是被墙了

### node-sass 安装失败的原因

npm 安装 node-sass 依赖时，会从 github 上下载 .node 文件。由于国内网络环境的问题，这个下载时间可能会很长，甚至导致超时失败。

解决方案就是使用其他源，或者使用工具下载，然后将安装源指定到本地。

#### 解决方法一：使用淘宝镜像源（推荐）

设置变量 sass_binary_site，指向淘宝镜像地址。示例：

```bash
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
# 或者设置全局镜像源
npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/

# 也可以设置系统环境变量的方式
# window 下
set SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ && npm install node-sass
# linux、mac 下
SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install node-sass
```
