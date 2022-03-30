---
title: Hubot + RocketChat 搭建
date: 2021-10-28 15:41:45
categories:
- 快速上手
tags:
- ChatOps
---

## 安装 MongoDB

参考 [Linux平台安装MongoDB](https://www.runoob.com/mongodb/mongodb-linux-install.html)

这里我们选择 tgz 下载，下载完安装包，并解压 **tgz**（以下演示的是 64 位 Linux上的安装） 。

```bash
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu1604-4.2.8.tgz    # 下载
tar -zxvf mongodb-linux-x86_64-ubuntu1604-4.2.8.tgz                                    # 解压

mv mongodb-src-r4.2.8  /usr/local/mongodb4                          # 将解压包拷贝到指定目录
```

MongoDB 的可执行文件位于 bin 目录下，所以可以将其添加到 **PATH** 路径中：

```bash
export PATH=<mongodb-install-directory>/bin:$PATH
```

**<mongodb-install-directory>** 为你 MongoDB 的安装路径。如本文的 **/usr/local/mongodb4** 。

```bash
export PATH=/usr/local/mongodb4/bin:$PATH
```

------

### 创建数据库目录

默认情况下 MongoDB 启动后会初始化以下两个目录：

- 数据存储目录：/var/lib/mongodb
- 日志文件目录：/var/log/mongodb

我们在启动前可以先创建这两个目录并设置当前用户有读写权限：

```bash
sudo mkdir -p /var/lib/mongo
sudo mkdir -p /var/log/mongodb
sudo chown `whoami` /var/lib/mongo     # 设置权限
sudo chown `whoami` /var/log/mongodb   # 设置权限
```

接下来启动 Mongodb 服务：

```bash
mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --fork
```

打开 /var/log/mongodb/mongod.log 文件看到以下信息，说明启动成功。

```bash
# tail -10f /var/log/mongodb/mongod.log
2020-07-09T12:20:17.391+0800 I  NETWORK  [listener] Listening on /tmp/mongodb-27017.sock
2020-07-09T12:20:17.392+0800 I  NETWORK  [listener] Listening on 127.0.0.1
2020-07-09T12:20:17.392+0800 I  NETWORK  [listener] waiting for connections on port 27017
```

------

### MongoDB 后台管理 Shell

如果你需要进入 mongodb 后台管理，你需要先打开 mongodb 装目录的下的 bin 目录，然后执行 mongo 命令文件。

MongoDB Shell 是 MongoDB 自带的交互式 Javascript shell，用来对 MongoDB 进行操作和管理的交互式环境。

当你进入 mongoDB 后台后，它默认会链接到 test 文档（数据库）：

```bash
$ cd /usr/local/mongodb4/bin
$ ./mongo
MongoDB shell version v4.2.8
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("2cfdafc4-dd56-4cfc-933a-187b887119b3") }
MongoDB server version: 4.2.8
Welcome to the MongoDB shell.
……
```

由于它是一个JavaScript shell，您可以运行一些简单的算术运算:

```bash
> 2+2
4
> 3+6
9
```

现在让我们插入一些简单的数据，并对插入的数据进行检索：

```bash
> db.runoob.insert({x:10})
WriteResult({ "nInserted" : 1 })
> db.runoob.find()
{ "_id" : ObjectId("5f069bdb4e02f8baf90f1184"), "x" : 10 }
> 
```

第一个命令将数字 10 插入到 runoob 集合的 x 字段中。

如果要停止 mongodb 可以使用以下命令：

```bash
mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --shutdown
```

也可以在 mongo 的命令出口中实现：

```bash
> use admin
switched to db admin
> db.shutdownServer()
```

更多安装方法可以参考官网：https://docs.mongodb.com/manual/administration/install-on-linux/

### 配置 mongod.conf

在 `/etc` 下创建文件 `mongod.conf` ：

```
# 数据库文件位置
dbpath=/var/lib/mongo

#日志文件的路径
logpath=/var/log/mongodb/mongod.log

#PID File 的完整路径
pidfilepath=/var/log/mongodb/master.pid

#设置每个数据库将被保存在一个单独的目录
#directoryperdb=true

#以追加的方式记录日志
#logappend=true

#IP和端口
bind_ip=127.0.0.1
port=27017

#以守护进程的方式运行，创建服务器进程
fork=true

#副本集名称，同一个副本集，名称必须一致
replSet=rs01
```

以 `mongod.conf` 启动

```bash
mongod --config /etc/mongod.conf --fork
```

停止

```bash
mongod --config /etc/mongod.conf --shutdown
```

## 安装 RocketChat

官网 [Rocket.Chat in CentOS](https://docs.rocket.chat/quick-start/installing-and-updating/manual-installation/centos)

参考 [使用centos7安装rocket.chat](https://blog.csdn.net/qq_38041439/article/details/104391064)

1. `Install necessary dependency packages` – 安装必须依赖的包

   `Update package list and configure yum to install the official MongoDB packages with the following yum repository file` – 更新你的 yum 包，并且安装 MongoDB

   ```bash
   sudo yum -y check-update
   ```

   ```bash
   cat << EOF | sudo tee -a /etc/yum.repos.d/mongodb-org-4.0.repo
   [mongodb-org-4.0]
   name=MongoDB Repository
   baseurl=https://repo.mongodb.org/yum/redhat/7/mongodb-org/4.0/x86_64/
   gpgcheck=1
   enabled=1
   gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
   EOF
   ```

   `Configure Node.js to be installed via package manager` – 通过 yum 的包管理器来安装 Node.js

   ```bash
   sudo yum install -y curl && curl -sL https://rpm.nodesource.com/setup_12.x | sudo bash -
   ```

   `Install build tools, MongoDB, nodejs and graphicsmagick` – 使用构建工具安装 MongoDB、nodejs 和 graphicsmagick

   ```bash
   sudo yum install -y gcc-c++ make mongodb-org nodejs
   ```

   ```bash
   sudo yum install -y epel-release && sudo yum install -y GraphicsMagick
   ```

   `Using npm install inherits and n, and the node version required by Rocket.Chat` – 使用 npm来安装依赖和切换到Rocket.Chat 要求的 node 的版本

   ```bash
   sudo npm install -g inherits n && sudo n 12.14.0
   ```

   **我之前已经装了 MongoDB 和 Nodejs ，不是用这里的方法安装的。不知道 GraphicsMagick 是不是 RocketChat 的依赖**

2. `Install Rocket.Chat` – 安装 Rocket.Chat
   `Download the latest Rocket.Chat version` –下载最新的 Rocket.Chat 的版本

   ```bash
   curl -L https://releases.rocket.chat/latest/download -o /tmp/rocket.chat.tgz
   ```

   ```bash
   tar -xzf /tmp/rocket.chat.tgz -C /tmp
   ```

   `Install (this guide uses /opt but feel free to choose a different directory)` – 安装（可以选择不同的目录）

   ```bash
   cd /tmp/bundle/programs/server && npm install
   ```

   ```bash
   sudo mv /tmp/bundle /opt/Rocket.Chat
   ```

3. `Configure the Rocket.Chat service` – Rocket.Chat 的配置
   `Add the rocketchat user, set the right permissions on the Rocket.Chat folder and create the Rocket.Chat service file` – 添加用户并创建正确的权限来创建 Rocket 的服务

   ```bash
   sudo useradd -M rocketchat && sudo usermod -L rocketchat
   ```

   ```bash
   sudo chown -R rocketchat:rocketchat /opt/Rocket.Chat
   ```

   ```bash
   cat << EOF |sudo tee -a /lib/systemd/system/rocketchat.service
   [Unit]
   Description=The Rocket.Chat server
   After=network.target remote-fs.target nss-lookup.target nginx.service mongod.service
   [Service]
   ExecStart=/usr/local/bin/node /opt/Rocket.Chat/main.js
   StandardOutput=syslog
   StandardError=syslog
   SyslogIdentifier=rocketchat
   User=rocketchat
   Environment=MONGO_URL=mongodb://localhost:27017/rocketchat?replicaSet=rs01 MONGO_OPLOG_URL=mongodb://localhost:27017/local?replicaSet=rs01 ROOT_URL=http://localhost:3000/ PORT=3000
   [Install]
   WantedBy=multi-user.target
   EOF
   ```

   使用 vi 修改刚刚写的 rocketchat.service 将 ROOT_URL 配置成你自己 ip 的 url 。

4. `Setup storage engine and replication for MongoDB (mandatory for versions > 1), and enable and start MongoDB and Rocket.Chat` – 设置 MongoDB 的存储和版本，并且启动 MongoDB 和 Rocket

   ```bash
   sudo sed -i "s/^#  engine:/  engine: mmapv1/"  /etc/mongod.conf
   ```

   ```bash
   sudo sed -i "s/^#replication:/replication:\n  replSetName: rs01/" /etc/mongod.conf
   ```

   ```bash
   sudo systemctl enable mongod && sudo systemctl start mongod
   ```

   ```bash
   mongo --eval "printjson(rs.initiate())"
   ```

   后台运行和启动 rocket

   ```bash
   sudo systemctl enable rocketchat && sudo systemctl start rocketchat
   ```

   查看当前 rocket 启动否成功

   ```bash
   sudo systemctl status rocketchat
   ```

   使用自己的 ip:3000 访问此地址进入 rocket 的配置页面完成配置登录进入聊天界面

   关闭rocket

   ```bash
   sudo systemctl stop rocketchat
   ```

## 安装 Hubot

下载 [hubot-rocketchat-boilerplate](https://github.com/RocketChat/hubot-rocketchat-boilerplate)

```bash
git clone https://github.com/RocketChat/hubot-rocketchat-boilerplate
cd hubot-rocketchat-boilerplate
npm install
```

创建一个 `.env` 文件：

```
export ROCKETCHAT_URL=myserver.com
export ROCKETCHAT_USER=mybotuser
export ROCKETCHAT_PASSWORD=mypassword
export ROCKETCHAT_ROOM=general
export ROCKETCHAT_USESSL=true
```

需要在 RocketChat 里给机器人创个号，上边的 `ROCKETCHAT_USER` 和 `ROCKETCHAT_PASSWORD` 就是机器人用户的账号密码

运行

```bash
source .env
bin/hubot
```

连接成功后在 RocketChat 里就能@机器人聊天了

```
mybotuser what time is it
```

