---
title: Git 常用命令
date: 2021-05-22 ‏‎19:00:55
categories:
- 快速上手
tags:
- Git
- Tools
---

## add

### 取消add操作

这里仅取消提交到库存区这个动作，只撤消动作，修改的文件不变

可以直接使用命令

```bash
git reset HEAD
```

这个是整体回到上次一次操作，绿字变红字(撤销add)

#### 撤消之前未add的所有修改

```bash
git reset --hard HEAD
```

## archive

直接在本地导出最新的版本库

```bash
git archive -o name-master.zip master
```

## 修改远程仓库地址

### 直接修改

1. 查看所有远程仓库

   ```bash
   git remote -v
   ```

2. ```bash
   git remote set-url origin http://x.x.x.x:xxxx/xxx/xxx.git
   ```

### 先删除再添加

1. 查看所有远程仓库

   ```bash
   git remote -v
   ```

2. 删除远程仓库地址

   ```bash
   git remote rm origin
   ```

3. ```bash
   git remote add origin http://x.x.x.x:xxxx/xxx/xxx.git
   ```

## 回滚

获取 revision_number

```bash
git reset --hard <revision_number>
```

`--hard` 会将代码回滚

再强制提交，需要较高权限

```bash
git push -f
```

## .gitignore

[.gitignore 文件模板](https://github.com/github/gitignore)

| 示例         | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| \#注释       | .gitignore 的注释                                            |
| *.txt        | 忽略所有 .txt 后缀的文件                                     |
| !src.a       | 忽略除 src.a 外的其他文件                                    |
| /todo        | 仅忽略项目根目录下的 todo 文件，不包括 src/todo              |
| build/       | 忽略 build/目录下的所有文件，过滤整个build文件夹             |
| doc/*.txt    | 忽略doc目录下所有 .txt 后缀的文件，但不包括 doc 子目录的 .txt 的文件 |
|              |                                                              |
| bin/         | 忽略当前路径下的 bin 文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件 |
| /bin         | 忽略根目录下的 bin 文件                                      |
| /*.c         | 忽略 cat.c，不忽略 build/cat.c                               |
| debug/*.obj  | 忽略debug/io.obj，不忽略 debug/common/io.obj和tools/debug/io.obj |
| **/foo       | 忽略/foo, a/foo, a/b/foo等                                   |
| a/**/b       | 忽略a/b, a/x/b, a/x/y/b等                                    |
| !/bin/run.sh | 不忽略bin目录下的run.sh文件                                  |
| config.js    | 忽略当前路径的 config.js 文件                                |
|              |                                                              |
| /mtk/        | 忽略整个文件夹                                               |
| /mtk/do.c    | 忽略某个具体文件                                             |
