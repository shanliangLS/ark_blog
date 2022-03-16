---
title: patch package 定制插件
date: 2022-03-16 14:06:00
categories:
- 前端
tags:
- npm
---

使用 patch-package 定制 node_modules 中的依赖包

<!-- more -->

- 参考[使用patch-package定制node_modules 中的依赖包](https://blog.csdn.net/qq_32429257/article/details/111051217)

## 使用背景

一般 npm 插件都会有预留参数和插槽等，但有时候仍无法避免个性化设置不够用需要修改插件源代码的情况，有两个通常的解决方法

1. 直接修改 node_modules 里的 package 代码。缺点是 node_modules 不便提交，只能自己改，项目组其他成员也不好同步，重新 npm install 后就没有了
2. 找到该插件的 github 项目地址，提一个 issue 或 PR。缺点是等待时间太久，有的需求太个性化了也不太好提，甚至有的插件作者已经不维护了

这时候就需要第三种高大上的方案：patch-package。   
该方案操作简单、便捷、且一劳永逸，便于同步。原理同第一种方案一样，不过是使用 patch-package 来修改 node_modules 中的依赖包，不需要手动改

## 使用

### 安装 patch-package

```bash
npm install patch-package --save-dev
```

### 修改 node_modules

在 node_modules 里找到需要修改的依赖包代码，以 `@femessage/log-viewer` 组件为例

{% asset_img patch_1.png %}

这里我修改了一下这个日志组件，加了点功能（可以在我的 [fork](https://github.com/shanliangLS/log-viewer) 中看看我改了什么，改完后可以运行项目测试一下有没有问题）

### 生成补丁

以 `@femessage/log-viewer` 组件为例

```bash
npx patch-package @femessage/log-viewer
```

该命令执行成功后会在项目根目录中自动创建一个 patches 文件夹，该文件夹中就会出现一个 package-name+version.patch 的补丁文件

{% asset_img patch_2.png %}

### 测试

手动删除项目中的 node_modules 文件，并重新执行 npm install 命令安装 node_modules 依赖包

安装成功后查看之前修改的 node_modules 依赖包中的文件，查看修改的代码是否依然存在，如果之前修改代码依然存在即表明补丁文件已经生效，可以将该补丁提交了
