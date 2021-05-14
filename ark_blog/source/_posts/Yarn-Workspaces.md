---
title: Yarn Workspaces
date: 2020-12-04 ‏‎9:48:01
categories:
- 前端
tags:
- Yarn
- Tools
---

*引用自 [https://classic.yarnpkg.com/zh-Hans/docs/workspaces](https://classic.yarnpkg.com/zh-Hans/docs/workspaces)*

工作区是设置你的软件包体系结构的一种新方式，默认情况下从 Yarn 1.0 开始使用。它允许你可以使用这种方式安装多个软件包， 就是只需要运行一次 ` yarn install ` 便可将所有依赖包全部安装。

## 你为什么要这么做？

- 你的依赖包可以链接在一起，这意味着你的工作区可以相互依赖，同时始终使用最新的可用代码。 这也是一个比 ` yarn link ` 更好的机制，因为它只影响你工作区的依赖树，而不会影响整个系统。
- 你所有的项目依赖将被安装在一起，这样可以让 Yarn 来更好地优化它们。
- Yarn 将使用一个单一的 lock 文件，而不是每个项目多有一个，这意味着更少的冲突和更容易进行代码检查。

## 如何使用它？

在 `package.json` 文件中添加以下内容，从现在开始，我们将此目录称为 “工作区根目录”：

**package.json**

```
{
  "private": true,
  "workspaces": ["workspace-a", "workspace-b"]
}
```

请注意，`private: true` 是必需的！工作区本身不应当被发布出去，所以我们添加了这个安全措施以确保它不会被意外暴露。

创建这个文件后，再创建两个名为 ` workspace-a ` 和 ` workspace-b ` 的子文件夹。 在每个文件夹里面，创建一个具有以下内容的 ` package. json ` 文件：

**workspace-a/package.json:**

```
{
  "name": "workspace-a",
  "version": "1.0.0",

  "dependencies": {
    "cross-env": "5.0.5"
  }
}
```

**workspace-b/package.json:**

```
{
  "name": "workspace-b",
  "version": "1.0.0",

  "dependencies": {
    "cross-env": "5.0.5",
    "workspace-a": "1.0.0"
  }
}
```

最后，在某个地方运行 ` yarn install ` ，当然最好是在工作区根目录里面。如果一切正常，你现在应该有一个类似这样的文件层次结构：

```
/package.json
/yarn.lock

/node_modules
/node_modules/cross-env
/node_modules/workspace-a -> /workspace-a

/workspace-a/package.json
/workspace-b/package.json
```

就是这样 ！ ` workspace-b `需要一个在` workspace-a `中的文件，现在将直接使用当前项目内部的文件，而不是直接去从 Github 上面取。 `cross-env ` 包已正确去重并放在项目的根目录下，让 ` workspace-a ` 和 ` workspace-b `可以一起使用这个包。

## 它与 Lerna 的对比怎么样？

Yarn 的工作区是诸如 Lerna 这样的工具可以（并且[正在](https://github.com/lerna/lerna/pull/899)）利用的底层机制。 它们将永远不会试图提供像 Lerna 那么高级的功能，但通过实现该解决方案的核心逻辑和 Yarn 内部的连接步骤，我们希望能够提供新的用法并提高性能。

## 提示和技巧

- `workspaces `字段是一个数组，其中包含到每个工作区的路径。 如果用这种方式来跟踪每个工作区可能是比较乏味的，所以这个字段也接受了通配符模式！ 例如，Babel 通过这个 ` packages/* ` 指令引用他们的所有包。

- 工作区的稳定性足以在 large-scale 应用程序中使用，并且不应改变常规安装的运行方式，但如果你认为它们正在破坏某些东西，则可以通过将以下内容添加到你的 Yarnrc 文件中来禁用它们：

  ```
  workspaces-experimental false
  ```

## 限制和警告

- 包层级在你的工作区和用户得到的内容之间将有所不同（工作区依赖将提升到文件系统层次结构中）。 对这个层级的假设已经是危险的，因为提升过程不是标准化的，所以理论上没有什么新东西。

- 在上面的示例中，如果 ` workspace-b ` 依赖于 ` workspace-a ` 的包，但是引用的是不同的版本，那么依赖包将从 Github 安装，而不是从本地文件系统链接。 这是因为一些软件包实际上需要使用以前的版本，以建立新的版本（Babel 是其中之一）。

- 在工作区中发布包时要留心。 如果你正准备发布下一个版本，并且你决定引用一个新依赖但忘了在 `package.json` 中声明，你的测试仍可能在本地通过（如果其他包已经把那个引用下载到了项目根目录）。 然而其他从源中拉取包的用户就不行了，由于依赖列表现在是不完整的，他们没办法下载那个新依赖。 目前没有办法在这种情况下抛出警告。

- 工作区必须是项目根目录的子目录。你不能也不应当引用位于项目目录之外的工作区。

- 目前尚不支持嵌套工作区。

## 清理环境

在依赖乱掉或者工程混乱的情况下，清理依赖

- 普通项目： 直接删除node_modules以及编译后的产物。
- monorepo： 不仅需要删除root的node_modules的编译产物还需要删除各个package里的node_modules以及编译产物
- 解决方式：使用lerna clean来删除所有的node_modules，使用yarn workspaces run clean来执行所有package的清理工作

```bash
lerna clean # 清理所有的node_modules
yarn workspaces run clean # 执行所有package的clean操作
```

## 安装|删除依赖

普通项目： 通过yarn add和yarn remove即可简单姐解决依赖库的安装和删除问题

monorepo: 一般分为三种场景

- 给某个package安装依赖：yarn workspace packageB add packageA 将packageA作为packageB的依赖进行安装
- 给所有的package安装依赖: 使用yarn workspaces add lodash 给所有的package安装依赖
- 给root 安装依赖：一般的公用的开发工具都是安装在root里，如typescript,我们使用yarn add -W -D typescript来给root安装依赖

对应的三种场景删除依赖如下

```bash
yarn workspace packageB remove packageA
yarn workspaces remove lodash
yarn remove -W -D typescript
```

> 对于安装local dependency，yarn的实现暂时有bug，第一次安装需要指明版本号，否则会安装失败如下
> 如果ui-button没有发布到npm则
> yarn workspace ui-form add ui-button 会安装失败，但是
> yarn workspace ui-form add ui-button@1.0.0会成功 ，详情见 [https://github.com/yarnpkg/yarn](https://link.zhihu.com/?target=https%3A//github.com/yarnpkg/yarn/issues/3973)
