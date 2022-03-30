---
title: Hubot + Slack 搭建
date: 2021-10-20 16:08:46
categories:
- 快速上手
tags:
- ChatOps
---

## 安装 Hubot

参考 [CetenOS 6.9 搭建hubot运维机器人](https://my.oschina.net/u/4418427/blog/4022356)

安装并升级 npm

```bash
yum install npm
npm install -g n 
n stable 
node -v
```

全局安装 hubot 生成程序

```bash
npm install -g hubot coffee-script yo generator-hubot
```

接下来是用 root 安装的话，这个时候可能会遇到很多权限上的错误，主要原因是它需要对于组有读写权限，所以我用一个普通用户来执行安装

```bash
su liangsh
cd /home/liangsh/src/
mkdir Hubot
cd Hubot
yo hubot
```

安装过程问你一些问题，Owner（拥有者）、Bot name（机器人的名字）、Description（描述你的公司），最后问你安装什么样的adapter，手工输入 `slack`

```bash
[liangsh@localhost Hubot]$ yo hubot
                     _____________________________  
                    /                             \ 
   //\              |      Extracting input for    |
  ////\    _____    |   self-replication process   |
 //////\  /_____\   \                             / 
 ======= |[^_/\_]|   /----------------------------  
  |   | _|___@@__|__                                
  +===+/  ///     \_\                               
   | |_\ /// HUBOT/\\                             
   |___/\//      /  \\                            
         \      /   +---+                            
          \____/    |   |                            
           | //|    +===+                            
            \//      |xx|                            

? Owner liangsh
? Bot name myHubot
? Description for test
? Bot adapter slack
```

如果直接不填回车，缺省安装的 hubot，会带上 heroku 和 redis ，如果用不到的话，可以卸载掉：（建议还是要按照上面的填，不然可能最后报错）

```bash
npm uninstall hubot-heroku-keepalive --save
```

在 hubot 目录下的 `external-scripts.json` 文件中把有关 heroku 和 redis 的两行删掉就行了，否则老是会报几个警告。也可以把 `hubot-scripts.json` 删了，会提示是空的：

```
Your hubot-scripts.json is empty, so you just need to remove it.
```

### 运行

```
./bin/hubot
```

目前只有输入一些特定的词汇才有反应

```bash
myHubot> myHubot ping
myHubot> PONG

myHubot> myHubot pug me
myHubot> https://user-images.githubusercontent.com/260/81488901-49615f80-923d-11ea-9d4b-1ab01525ecea.jpg?006eb295
```

### 增加一段简单的脚本

在 scripts 文件夹下有一个 `example.coffee` 文件，里面有一些注释了的示例脚本

在 `module.exports = (robot) ->` 下增加两行

```js
robot.hear /你是谁/i, (res) ->
  res.send "我是重大好"
```

{% asset_img hubot_1.png %}

再启动 hubot 就能有相应对话了

```bash
myHubot> 你是谁
myHubot> 我是重大好
```

### 运行 bash 脚本

仅仅使用 node 环境下的 script 脚本是不足以完成繁重的运维工作的，此时我们还需要 hubot 能够运行 bash 脚本。这个任务也很容易完成，只要添加一个插件：[hubot-script-shellcmd](https://github.com/coderofsalvation/hubot-script-shellcmd)。

安装：

```bash
# install
cd <yourhubotdir>
npm install hubot-script-shellcmd

# optional: separate your shellcmds from the npm module
cp -R node_modules/hubot-script-shellcmd/bash .

# run bot
(add 'hubot-script-shellcmd' to external-scripts.json if needed)
```

使用：

```bash
you> hubot shellcmd

hubot> Available commands:
hubot>   helloworld

you> hubot shellcmd helloworld 
hubot> helloworld
```

我这输 `shellcmd` 没反应，我还以为我哪里装错了或者权限问题，找了半天原因，最后运行机器人的 `help` 命令才发现给改名成了 `shell`，我明明没做其它任何配置命令名却不一样 (￣ε(#￣)☆╰╮o(￣皿￣///)浪费了我半天时间 

`bash/handlers/` 文件夹里已经有两个 `helloworld` 和 `update` ，可以在 `bash/handlers/` 下随便创建一个文件 `who` ：

```bash
#!/bin/bash
echo "I , robot!"
sleep 1s;
echo "I , dashbot!"

exit 0
```

需要赋值可执行权限：

```bash
chmod 775 bash/handlers/who
```

然后就能在机器人那执行了

## 配置 Slack

参考 [Hubot—打造机器人助手](https://www.jianshu.com/p/55a05b4a9711)

在 Slack 官网注册账号，一开始用 google 账号试了几天，总是创建不了工作区，我还以为是网络或权限问题，后来发现 Slack 不允许使用 qq 邮箱（我 google 账号是用 qq 邮箱注册的），甚至不允许使用163网易邮箱，最后用的 outlook 邮箱才登进去。

从聊天界面进入到 `APP Directory`

{% asset_img slack_1.png %}

搜索 `Hubot`

{% asset_img slack_2.png %}

安装 hubot 的过程中，要给机器人起个名字、填写基本信息，最重要的是获取后面要用到的 API Token

{% asset_img slack_3.png %}

到这里，slack 的配置就完成了，接下来在本地启动 hubot ，并连接到 slack

### 连接 hubot 和 slack

在命令行输入下面指令：

```bash
HUBOT_SLACK_TOKEN=<API Token> ./bin/hubot --adapter slack
```

这里的 `<API Token>` 就是上文在 Slack 中配置的

连接成功后显示如下：

```bash
[liangsh@localhost Hubot]$ HUBOT_SLACK_TOKEN=xxxx ./bin/hubot --adapter slack
audited 160 packages in 11.723s

2 packages are looking for funding
  run `npm fund` for details

found 1 low severity vulnerability
  run `npm audit fix` to fix them, or `npm audit` for details
[Wed Oct 20 2021 22:08:33 GMT-0400 (Eastern Daylight Time)] INFO hubot-slack adapter v4.9.0
warn: SlackDataStore is deprecated and will be removed in the next major version. See project documentation for a migration guide.
warn: SlackDataStore is deprecated and will be removed in the next major version. See project documentation for a migration guide.
[Wed Oct 20 2021 22:08:34 GMT-0400 (Eastern Daylight Time)] INFO Logged in as @dashbot in workspace Hubot测试-ls
[Wed Oct 20 2021 22:08:37 GMT-0400 (Eastern Daylight Time)] INFO Connected to Slack RTM
```

### 和机器人聊天

接下来我们在聊天窗口和机器人聊天，对话如下：

{% asset_img slack_4.png %}