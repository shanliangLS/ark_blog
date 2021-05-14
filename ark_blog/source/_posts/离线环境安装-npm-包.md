---
title: 离线环境安装 npm 包
date: 2020-12-08 22:06:16
categories:
- 前端
tags:
- npm
- Tools
---

在使用 node 时，如要用第三方库，可使用 `npm install` 命令安装。它会自动从网上的 npm 库中下载对应的包文件，比较方便。 有些情况下，或在有些公司中，开发环境无法直接连接外网，只能在离线环境下使用开发。就不能直接使用 npm 包管理工具通过网络下载安装依赖库。

这里介绍的方法是将依赖库制作成为一个 tar 文件，然后直接使用 `npm install xxxx.tgz` ，离线安装这个依赖库文件。

## 制作 tar 包

首先在有互联网连接的环境的情况自作依赖库的 tar 包，步骤如下：

1. **安装 npm-pack-all 工具**

   `npm-pack-all` 可以将 npm 库文件打包为一个 tar 文件，访问 https://www.npmjs.com/package/npm-pack-all 了解更多。

   执行下面命令安装它：

   ```bash
   npm install -g npm-pack-all
   ```

2. **下载你要打包的库**

   一个 npm 库通常又会依赖其它的 npm 库。为了离线使用，它和它所有的依赖库都需要打包起来。我们现在以 "vuepress-plugin-vssue" 为例介绍在 Windows 操作系统上的打包过程。

   这是一个自动化 web 网站常用的库，通常装在自动化脚本所在的本地目录。这里为了打包，需要全局安装。运行下面的命令全局安装这个库：

   ```bash
   npm install -g @vssue/vuepress-plugin-vssue
   ```

   使用 -g 参数安装会自动把 vuepress-plugin-vssue 依赖的包放在全局目录下，即 `%userprofile%\AppData\Roaming\npm`。类似这样:

   {% asset_img npm-pack-all1.png %}

   从上图中可以看到，全局安装的特点是，这个包的所有依赖包都安装在了自己的目录下的node_modules子目录里。

3. **制作 tgz 文件**

   - 在命令行下，通过 `cd %appdata%\npm\node_modules\@vssue\api-gitlab-v4` 调整当前目录到 api-gitlab-v4 目录下。
   - 执行 `npm-pack-all`
   - 执行完毕后，会在当前目录下生成 `.tgz` 文件，这里生成的文件为 vssue-vuepress-plugin-vssue-1.4.6.tgz 。根据你实际安装的 npm 包文件版本不同，这个文件有可能不同。
   