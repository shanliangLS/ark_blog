# Hubot 官方文档

[原文链接🔗](https://hubot.github.com/docs/)

[翻译链接🔗](https://blog.xyang.xin/hubot-document/)

![hubot-avatar.png](https://images.xyang.xin/2021%2F09%2F01%2Fhubot-avatar.png)

## 概览

### Hubot 起步

您需要 [node.js 和 npm](https://docs.npmjs.com/getting-started/installing-node)。安装它们后，我们就可以安装 hubot 生成器：

```
$ npm install -g yo generator-hubot
```

这条命令将给我们带来 hubot [yeoman](http://yeoman.io/) 生成器。现在我们就可以先创建一个的文件夹，然后在里面生成一个新的 hubot 实例。例如，如果我们想制造一个叫 myhubot 的机器人：

```
$ mkdir myhubot
$ cd myhubot
$ yo hubot
```

执行命令后，您将被问到一些关于谁在创建机器人和您将使用哪个适配器的问题。适配器是 hubot 与不同聊天提供商集成的方式。

如果您希望在没有交互提示配置的情况下自动化您的 hubot 构建，您可以将以下选项添加到 `yo hubot` 命令中：

| Option                                                       | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| --owner=”Bot Wrangler [bw@example.com](mailto:bw@example.com)“ | Bot owner, e.g. “Bot Wrangler [bw@example.com](mailto:bw@example.com)” |
| --name=”Hubot”                                               | bot 名，e.g. “Hubot”                                         |
| --description=”Delightfully aware robutt”                    | bot 描述，e.g. “Delightfully aware robutt”                   |
| --adapter=campfire                                           | bot 适配器，e.g. “campfire”                                  |
| --defaults                                                   | 所有声明都设置为默认值且无需提示                             |

您现在就拥有了自己的一个多功能的 hubot！为方便起见，有一个 `bin/hubot` 命令，用于处理安装 npm 依赖项、加载脚本，然后启动您的 hubot。

Hubot 需要 Redis 来持久化数据，因此在您可以在自己的计算机上启动 hubot 之前，您应该在本地主机上安装 Redis。如果只是想在没有 Redis 的情况下测试 Hubot，那么您可以从 `external-scripts.json` 中删除 `hubot-redis-brain`。

```
$ bin/hubot
Hubot>
```

这将使用 [shell 适配器](https://hubot.github.com/docs/adapters/shell/)启动 hubot，这对开发非常有用。记下 `Hubot>`，这是您的 hubot 将用命令响应的名称。例如，列出可用命令：

```
$ bin/hubot
hubot> hubot help
hubot adapter - Reply with the adapter
hubot animate me <query> - The same thing as `image me`, except adds a few parameters to try to return an animated GIF instead.
hubot echo <text> - Reply back with <text>
hubot help - Displays all of the help commands that hubot knows about.
hubot help <query> - Displays all help commands that match <query>.
hubot image me <query> - The Original. Queries Google Images for <query> and returns a random top result.
hubot map me <query> - Returns a map view of the area returned by `query`.
hubot mustache me <query> - Searches Google Images for the specified query and mustaches it.
hubot mustache me <url> - Adds a mustache to the specified URL.
hubot ping - Reply with pong
hubot pronounce <phrase> in <language> - Provides pronunciation of <phrase> (<language> is optional)
hubot pug bomb N - get N pugs
hubot pug me - Receive a pug
hubot the rules - Make sure hubot still knows the rules.
hubot time - Reply with current time
hubot translate me <phrase> - Searches for a translation for the <phrase> and then prints that bad boy out.
hubot translate me from <source> into <target> <phrase> - Translates <phrase> from <source> into <target>. Both <source> and <target> are optional
hubot youtube me <query> - Searches YouTube for the query and returns the video embed link.
ship it - Display a motivation squirrel
```

您基本上都会想要改变您的 hubot 的名字来添加角色。 bin/hubot 需要一个 `--name`：

```
% bin/hubot --name myhubot
myhubot>
```

您的 hubot 现在将响应 `myhubot` 。这是不区分大小写的，可以以 `@` 为前缀或以 `:` 为后缀。这些是等效的：

```
MYHUBOT help
myhubot help
@myhubot help
myhubot: help
```

### 脚本

Hubot 的力量来自于脚本。社区编写和维护着几百个脚本。通过使用 `hubot-scripts <your-search-term>` 查找 [NPM registry](https://www.npmjs.com/browse/keyword/hubot-scripts) 找到它们。例如：

```
$ npm search hubot-scripts github
NAME                  DESCRIPTION
hubot-deployer        Giving Hubot the ability to deploy GitHub repos to PaaS providers hubot hubot-scripts hubot-gith
hubot-gh-release-pr   A hubot script to create GitHub's PR for release
hubot-github          Giving Hubot the ability to be a vital member of your github organization
…
```

要使用 NPM 包中的脚本：

1. 运行 `npm install --save <package-name>` 去添加该包作为依赖并安装它。
2. 添加该包到 `external-scripts.json`。
3. 运行 `npm home <package-name>` 以打开脚本主页的浏览器窗口，您可以在其中找到有关配置和安装脚本的更多信息。

您也可以将您自己的脚本放在 `scripts/` 目录下。放在这里的所有脚本都将被 hubot 自动加载和使用。阅读有关通过[编写自己的脚本](https://hubot.github.com/docs/scripting/)自定义 hubot 的更多信息。

### 适配器

Hubot 使用适配器模式来支持多个聊天后端。这是[可用适配器的列表](https://hubot.github.com/docs/adapters/)，以及如何配置它们的详细信息。

### 部署

您可以将 hubot 部署到 Heroku，这是官方支持的方法。此外，您还可以将 hubot 部署到类似 UNIX 的系统或 Windows 中。请注意，部署到 Windows 的支持尚未得到官方支持。

- [部署 hubot 到 Heroku](https://hubot.github.com/docs/deploying/heroku/)
- [部署 hubot 到 UNIX](https://hubot.github.com/docs/deploying/unix/)
- [部署 hubot 到 Windows](https://hubot.github.com/docs/deploying/windows/)

### 模式

使用自定义脚本，您可以快速自定义 hubot，使他（她）成为最具生命力的机器人。当您教您的 hubot 新的技能，阅读 [docs/patterns.md](https://hubot.github.com/docs/patterns/) 里的一些漂亮的技巧，可能会派上用场。

## 脚本

开箱即用的 hubot 不会做太多， 但它是一个可扩展的，可编脚本的机器人朋友。有[数以百计的脚本由社区编写和维护](https://hubot.github.com/docs/#scripts)，并且自己写起来也很容易。您可以在 hubot 的 `scripts` 目录中创建自定义脚本，或[创建一个脚本包](https://hubot.github.com/docs/scripting/#creating-a-script-package)，以便与社区共享！

### 脚本剖析

当您创建 hubot 时，生成器还创建了 `scripts` 目录。如果您看了一下那里，您会看到一些脚本的例子。脚本要成为脚本，它需要：

- 在 hubot 脚本加载的目录里（默认为 `src/scripts` 和 `scripts`）
- 是一个`.coffee` 或者`.js` 文件
- 导出一个函数

导出一个函数，我们的意思是：

```
module.exports = (robot) ->
  # your code here
```

机器人参数是您的机器人朋友的实例。在这一点上，我们可以开始编写一些很棒的脚本。

### 聆听和响应

由于这是一个聊天机器人，最常见的交互是基于消息。Hubot 可以 `hear` 在房间里说的消息，或直接 `response` 专门传达给它的消息。这两种方法都以一个正则表达式和一个回调函数作为参数。例如：

```
module.exports = (robot) ->
  robot.hear /badger/i, (res) ->
    # your code here

  robot.respond /open the pod bay doors/i, (res) ->
    # your code here
```

每当一个消息的文本匹配时，`robot.hear /badger/` 的回调就会被调用。例如：

- Stop badgering the witness
- badger me
- what exactly is a badger anyways

`robot.respond /open the pod bay doors/i` 的回调仅当消息紧跟着机器人的名字或者别名被调用。如果机器人的名称是 HAL，别名是 /，则此回调将被以下触发：

- hal open the pod bay doors
- HAL: open the pod bay doors
- @HAL open the pod bay doors
- /open the pod bay doors

它不会被调用：

- HAL: please open the pod bay doors
  - 因为它的响应与机器人名称后紧接着的文本绑定
- has anyone ever mentioned how lovely you are when you open the pod bay doors?
  - 因为它确少机器人的名字

### 发送和回复

`res` 参数是 `Response` 的实例（在历史上，这个参数是 `msg`，您可能会看到其他脚本使用它）。有了它，您可以将消息 `send` 回 `res` 来的房间，`emote` 一个消息到一个房间（如果给定的适配器支持它），或 `reply` 发送消息的人。例如：

```
module.exports = (robot) ->
  robot.hear /badger/i, (res) ->
    res.send "Badgers? BADGERS? WE DON'T NEED NO STINKIN BADGERS"

  robot.respond /open the pod bay doors/i, (res) ->
    res.reply "I'm afraid I can't let you do that."

  robot.hear /I like pie/i, (res) ->
    res.emote "makes a freshly baked pie"
```

`robot.hear /badgers/` 的回调发送指定的消息，而不管向谁说： “Badgers? BADGERS? WE DON’T NEED NO STINKIN BADGERS”。

如果一个用户 Dave 说 “HAL: open the pod bay doors”， `robot.respond /open the pod bay doors/i` 的回调发送一个消息 `Dave: I’m afraid I can’t let you do that.`

### 给房间或用户的消息

消息可以使用 `messageRoom` 功能发送给指定的房间或用户。

```
module.exports = (robot) ->

  robot.hear /green eggs/i, (res) ->
    room = "mytestroom"
    robot.messageRoom room, "I do not like green eggs and ham.  I do not like them sam-I-am."
```

如果需要，可以明确指定用户名，或使用响应对象，可以向原始发件人发送私人消息。

```
robot.respond /I don't like Sam-I-am/i, (res) ->
  room =  'joemanager'
  robot.messageRoom room, "Someone does not like Dr. Seus"
  res.reply  "That Sam-I-am\nThat Sam-I-am\nI do not like\nthat Sam-I-am"

robot.hear /Sam-I-am/i, (res) ->
  room =  res.envelope.user.name
  robot.messageRoom room, "That Sam-I-am\nThat Sam-I-am\nI do not like\nthat Sam-I-am"
```

### 捕获数据

到目前为止，我们的脚本已经有静态响应，这虽然有趣，在功能方面却很薄弱。`res.match` 的结果是将传入的消息与正则表达进行匹配。这只是一个 [JavaScript thing](http://www.w3schools.com/jsref/jsref_match.asp)， 它最终是一个数组， 索引 0 是与表达式匹配的全文。如果包括捕获组，这些组将填充 `res.match`。例如，如果我们更新脚本：

```
robot.respond /open the (.*) doors/i, (res) ->
    # your code here
```

如果 Dave 说 “HAL: open the pod bay doors”，`res.match[0]` 就成了 `open the pod bay doors`，`res.match[1]` 则为 `pod bay`。现在我们可以开始做更多动态的东西了：

```
robot.respond /open the (.*) doors/i, (res) ->
  doorType = res.match[1]
  if doorType is "pod bay"
    res.reply "I'm afraid I can't let you do that."
  else
    res.reply "Opening #{doorType} doors"
```

### 调用 HTTP

Hubot 可以为您进行 HTTP 请求，以集成和整合第三方 API 。这能够通过一个在 `robot.http` 的 [node-scoped-http-client](https://github.com/technoweenie/node-scoped-http-client) 实例进行。最简单的例子如：

```
robot.http("https://midnight-train")
    .get() (err, res, body) ->
      # your code here
```

post 请求例如：

```
data = JSON.stringify({
  foo: 'bar'
})
robot.http("https://midnight-train")
  .header('Content-Type', 'application/json')
  .post(data) (err, res, body) ->
    # your code here
```

如果遇到错误，`err` 就是您所遇到的错误。您通常会想要检查这一点，并相应地处理：

```
robot.http("https://midnight-train")
  .get() (err, res, body) ->
    if err
      res.send "Encountered an error :( #{err}"
      return
    # your code here, knowing it was successful
```

`res` 是 node 的 [http.ServerResponse](http://nodejs.org/api/http.html#http_class_http_serverresponse) 的一个实例。使用 node-scoped-http-client 的时候大多数方法不是特别关键， 但是有意思的是 `statusCode` 和 `getHeader`。使用 `statusCode` 去检查 HTTP 的状态码，当不是 200 状态码的时候意味着糟糕的事情发生了。使用 `getHeader` 查看 header，例如去检查服务限流（`X-RateLimit-Limit:1 //每秒1次请求`）：

```
robot.http("https://midnight-train")
  .get() (err, res, body) ->
    # pretend there's error checking code here

    if res.statusCode isnt 200
      res.send "Request didn't come back HTTP 200 :("
      return

    rateLimitRemaining = parseInt res.getHeader('X-RateLimit-Limit') if res.getHeader('X-RateLimit-Limit')
    if rateLimitRemaining and rateLimitRemaining < 1
      res.send "Rate Limit hit, stop believing for awhile"

    # rest of your code
```

`body` 是响应体字符串，应该是您最关心的东西。

```
robot.http("https://midnight-train")
  .get() (err, res, body) ->
    # error checking code here

    res.send "Got back #{body}"
```

#### JSON

与 API 交流，最简单的方法是 JSON，因为它不需要任何额外的依赖。当使用 `robot.http` 请求，您通常应该设置 `Accept` 头去给 API 一个希望得到什么的线索。一旦您得到了返回的 `body`，您就可以使用 `JSON.parse`：

```
robot.http("https://midnight-train")
  .header('Accept', 'application/json')
  .get() (err, res, body) ->
    # error checking code here

    data = JSON.parse body
    res.send "#{data.passenger} taking midnight train going #{data.destination}"
```

得到的返回也可能不是 JSON，就像如果这 API 遇到一个错误并且它试图渲染一个正常的 HTML 错误，而不是 JSON。为了安全起见，您应该检查内容类型，并在解析时发现任何错误。

```
robot.http("https://midnight-train")
  .header('Accept', 'application/json')
  .get() (err, res, body) ->
    # err & response status checking code here

    if response.getHeader('Content-Type') isnt 'application/json'
      res.send "Didn't get back JSON :("
      return

    data = null
    try
      data = JSON.parse body
    catch error
     res.send "Ran into an error parsing JSON :("
     return

    # your code here
```

#### XML

XML API 比较难，因为没有捆绑的 XML 解析库。详细介绍此文档的范围超出了此范围，但下面有一些可以查看的库：

- [xml2json](https://github.com/buglabs/node-xml2json) (simplest to use, but has some limitations)
- [jsdom](https://github.com/tmpvar/jsdom) (JavaScript implementation of the W3C DOM)
- [xml2js](https://github.com/Leonidas-from-XIV/node-xml2js)

#### 萤幕抓取

对于没有 API 的时代，总有可能进行萤幕抓取。详细介绍此文档的范围超出了此范围，但下面是一些可以查看的库：

- [cheerio](https://github.com/MatthewMueller/cheerio) (familiar syntax and API to jQuery)
- [jsdom](https://github.com/tmpvar/jsdom) (JavaScript implementation of the W3C DOM)

#### 高级 HTTP 和 HTTPS 设置

前面提到，hubot 使用 [node-scoped-http-client](https://github.com/technoweenie/node-scoped-http-client) 来提供一个简单的接口去发起 HTTP 和 HTTPS 请求。它封装了 node 的 [http](http://nodejs.org/api/http.html) 和 [https](http://nodejs.org/api/https.html) 库，但为常见的交互类型提供了一个简单的 DSL（Domain Specific Language 领域专用语言）。

如果您需要直接控制 http 和 https 的选项，您传第二个参数给 `robot.http`，它将通过 node-scoped-http-client 传递给 http 或者 https：

```
options =
  # don't verify server certificate against a CA, SCARY!
  rejectUnauthorized: false
robot.http("https://midnight-train", options)
```

另外，如果 node-scoped-http-client 并不适合您，您可以直接使用 [http](http://nodejs.org/api/http.html) 和 [https](http://nodejs.org/api/https.html)，或者其它的 node 库，例如：[request](https://github.com/request/request)

### 随机

一种常见的模式是聆听或响应命令，并发送随机有趣的图像或文本从一个包含可能性的数组。直接使用 JavaScript 和 CoffeeScript 来实现比较麻烦， 所以 hubot 包括一个方便的方法：

```
lulz = ['lol', 'rofl', 'lmao']

res.send res.random lulz
```

### 主题

Hubot 可以响应一个房间的主题变化，假设适配器支持它：

```
module.exports = (robot) ->
  robot.topic (res) ->
    res.send "#{res.message.text}? That's a Paddlin'"
```

### 加入和离开

Hubot 能够观察用户的加入和离开，假设适配器支持它。

```
enterReplies = ['Hi', 'Target Acquired', 'Firing', 'Hello friend.', 'Gotcha', 'I see you']
leaveReplies = ['Are you still there?', 'Target lost', 'Searching']

module.exports = (robot) ->
  robot.enter (res) ->
    res.send res.random enterReplies
  robot.leave (res) ->
    res.send res.random leaveReplies
```

### 自定义监听器

虽然上面的 helpers 涵盖了普通用户需要的大部分功能（聆听、响应、加入、离开、主题），有时您想为监听器赋予特定的匹配逻辑。如果是这样，您可以使用 `listen` 来指定自定义匹配函数来替代正则表达。

如果监听器回调要被执行，匹配函数必须返回真实值。然后将匹配函数的真实返回值用 `response.match` 传递给回调。

```
module.exports = (robot) ->
  robot.listen(
    (message) -> # Match function
      # Occassionally respond to things that Steve says
      message.user.name is "Steve" and Math.random() > 0.8
    (response) -> # Standard listener callback
      # Let Steve know how happy you are that he exists
      response.reply "HI STEVE! YOU'RE MY BEST FRIEND! (but only like #{response.match * 100}% of the time)"
  )
```

有关复杂的匹配器示例，请参阅[设计模式文档](https://hubot.github.com/docs/patterns/)。

### 环境变量

Hubot 可以使用 [process.env](http://nodejs.org/api/process.html#process_process_env) 访问他运行的环境，就像任何其他 node 程序一样。这可用于配置脚本的运行方式，约定使用 `HUBOT_`前缀。

```
answer = process.env.HUBOT_ANSWER_TO_THE_ULTIMATE_QUESTION_OF_LIFE_THE_UNIVERSE_AND_EVERYTHING

module.exports = (robot) ->
  robot.respond /what is the answer to the ultimate question of life/, (res) ->
    res.send "#{answer}, but what is the question?"
```

小心的去确保环境变量没有定义时脚本可以加载，给 hubot 开发者去定义该环境变量的提示，或者默认的一些东西。由脚本编写者来决定这是否应该是一个致命的错误（e.g. hubot 退出），也可以制作任何依赖于它的脚本，去表明它需要配置。在尽可能和有意义时，脚本运作不需要任何其它的配置更好。

在这里，我们可以默认的东西：

```
answer = process.env.HUBOT_ANSWER_TO_THE_ULTIMATE_QUESTION_OF_LIFE_THE_UNIVERSE_AND_EVERYTHING or 42

module.exports = (robot) ->
  robot.respond /what is the answer to the ultimate question of life/, (res) ->
    res.send "#{answer}, but what is the question?"
```

如果环境变量没有定义，在这里我们退出：

```
answer = process.env.HUBOT_ANSWER_TO_THE_ULTIMATE_QUESTION_OF_LIFE_THE_UNIVERSE_AND_EVERYTHING
unless answer?
  console.log "Missing HUBOT_ANSWER_TO_THE_ULTIMATE_QUESTION_OF_LIFE_THE_UNIVERSE_AND_EVERYTHING in environment: please set and try again"
  process.exit(1)

module.exports = (robot) ->
  robot.respond /what is the answer to the ultimate question of life/, (res) ->
    res.send "#{answer}, but what is the question?"
```

最后， 我们更新 `robot.repond` 去检查它：

```
answer = process.env.HUBOT_ANSWER_TO_THE_ULTIMATE_QUESTION_OF_LIFE_THE_UNIVERSE_AND_EVERYTHING

module.exports = (robot) ->
  robot.respond /what is the answer to the ultimate question of life/, (res) ->
    unless answer?
      res.send "Missing HUBOT_ANSWER_TO_THE_ULTIMATE_QUESTION_OF_LIFE_THE_UNIVERSE_AND_EVERYTHING in environment: please set and try again"
      return
    res.send "#{answer}, but what is the question?"
```

### 依赖

Hubot 使用 [npm](https://github.com/isaacs/npm) 去管理它的依赖。要去添加额外的包，添加它们到 `package.json` 文件的 `dependencies` 中。例如，添加 `lolimadeupthispackage 1.2.3`，如：

```
"dependencies": {
  "hubot":         "2.5.5",
  "lolimadeupthispackage": "1.2.3"
},
```

如果您使用 hubot-scripts 中的脚本，记下要添加的脚本中的 `Dependencies` 文档。它们以可以复制粘贴进 `package.json` 的格式列出，只需确保根据需要添加逗号使其成为有效 JSON 即可。

### 超时和间隔

> 译者注：这里普及 JavaScript 的 `setTimeout` 和 `setInterval` 语法。

Hubot 可以使用 JavaScript 内置的 [setTimeout](http://nodejs.org/api/timers.html#timers_settimeout_callback_delay_arg) 来延迟运行代码。

```
module.exports = (robot) ->
  robot.respond /you are a little slow/, (res) ->
    setTimeout () ->
      res.send "Who you calling 'slow'?"
    , 60 * 1000
```

另外，hubot 可以用 [setInterval](http://nodejs.org/api/timers.html#timers_setinterval_callback_delay_arg) 来间隔运行代码。它需要回调方法，以及调用之间的等待时间：

```
module.exports = (robot) ->
  robot.respond /annoy me/, (res) ->
    res.send "Hey, want to hear the most annoying sound in the world?"
    setInterval () ->
      res.send "AAAAAAAAAAAEEEEEEEEEEEEEEEEEEEEEEEEIIIIIIIIHHHHHHHHHH"
    , 1000
```

`setTimeout` 和 `setInterval` 创建时，都返回超时或间隔的 ID。这可用于 `clearTimeout` 和 `clearInterval`。

```
module.exports = (robot) ->
  annoyIntervalId = null

  robot.respond /annoy me/, (res) ->
    if annoyIntervalId
      res.send "AAAAAAAAAAAEEEEEEEEEEEEEEEEEEEEEEEEIIIIIIIIHHHHHHHHHH"
      return

    res.send "Hey, want to hear the most annoying sound in the world?"
    annoyIntervalId = setInterval () ->
      res.send "AAAAAAAAAAAEEEEEEEEEEEEEEEEEEEEEEEEIIIIIIIIHHHHHHHHHH"
    , 1000

  robot.respond /unannoy me/, (res) ->
    if annoyIntervalId
      res.send "GUYS, GUYS, GUYS!"
      clearInterval(annoyIntervalId)
      annoyIntervalId = null
    else
      res.send "Not annoying you right now, am I?"
```

### HTTP 监听器

Hubot 包括支持 [express](http://expressjs.com/) 网络框架以满足 HTTP 请求。它监听 `EXPRESS_PORT` 或 `PORT` 环境变量指定的端口（按该顺序首选），默认值设置为 8080。一个 express 应用的实例可在 `robot.router` 中。可以通过指定 `EXPRESS_USER` 和 `EXPRESS_PASSWORD` 来保护用户名和密码。通过设置 `EXPRESS_STATIC` 它可以自动为静态文件服务。

最常见的用途是为带有 webhooks 的服务提供 HTTP 端点去推送，并让这些出现在聊天中。

```
module.exports = (robot) ->
  # the expected value of :room is going to vary by adapter, it might be a numeric id, name, token, or some other value
  robot.router.post '/hubot/chatsecrets/:room', (req, res) ->
    room   = req.params.room
    data   = if req.body.payload? then JSON.parse req.body.payload else req.body
    secret = data.secret

    robot.messageRoom room, "I have a secret: #{secret}"

    res.send 'OK'
```

用 curl 测试它；另请参阅下面关于 [error handling](https://hubot.github.com/docs/scripting/#error-handling) 的部分。

```
// raw json, must specify Content-Type: application/json
curl -X POST -H "Content-Type: application/json" -d '{"secret":"C-TECH Astronomy"}' http://127.0.0.1:8080/hubot/chatsecrets/general

// defaults Content-Type: application/x-www-form-urlencoded, must st payload=...
curl -d 'payload=%7B%22secret%22%3A%22C-TECH+Astronomy%22%7D' http://127.0.0.1:8080/hubot/chatsecrets/general
```

所有的端点 URLs 应该以 `/hubot` 起头（无论您的机器人及叫什么名字）。这种一致性使得设置 webhooks（可复制粘贴的 URL）更加容易，并且保证 URLs 是有效的（并不是所有的 bot 命名都是 URL 安全的）。

### 事件

Hubot 还可以响应可用于在脚本之间传递数据的事件。这是通过使用 `robot.emit` 和 `robot.on` 封装 node.js 的 [EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter) 实现的。

其中一个用例是，有一个脚本用于处理与服务的交互，然后在事件出现时发送。例如，我们有一个脚本从 GitHub `post-commit` 钩子接收数据，当 commits 来时发送 commits 事件，然后对这些提交进行另一个脚本行为。

```
# src/scripts/github-commits.coffee
module.exports = (robot) ->
  robot.router.post "/hubot/gh-commits", (req, res) ->
    robot.emit "commit", {
        user    : {}, #hubot user object
        repo    : 'https://github.com/github/hubot',
        hash  : '2e1951c089bd865839328592ff673d2f08153643'
    }
# src/scripts/heroku.coffee
module.exports = (robot) ->
  robot.on "commit", (commit) ->
    robot.send commit.user, "Will now deploy #{commit.hash} from #{commit.repo}!"
    #deploy code goes here
```

如果您提供一个事件，强烈建议在其数据中包括 hubot user 或 room 对象。这将允许 hubot 在聊天中通知用户或房间。

### 错误处理

没有完美的代码，并且错误和意外是意料之中的。以前，一个未捕获的异常会是您的 hubot 实例崩溃。Hubot 现在包含了一个 `uncaughtException` 处理程序，它为脚本提供钩子以此来进行错误处理。

```
# src/scripts/does-not-compute.coffee
module.exports = (robot) ->
  robot.error (err, res) ->
    robot.logger.error "DOES NOT COMPUTE"

    if res?
      res.reply "DOES NOT COMPUTE"
```

您可以在这里做任何您想做的事，但您会想采取额外的预防措施来拯救和记录错误，特别是异步代码。否则，您可能会发现自己有递归的错误，却不知道发生了什么事情。

在头巾之下，‘error’事件发射出来，伴随着的是错误处理程序消费那个事件。`uncaughtException` 处理程序，[从技术上讲， 使过程处于未知状态。](http://nodejs.org/api/process.html#process_event_uncaughtexception)。因此，无论何时您应该尽可能的拯救您自己的异常，并且自己发出它们。第一个参数是发出的错误，第二个参数是生成错误的可选消息。
使用前面的例子：

```
robot.router.post '/hubot/chatsecrets/:room', (req, res) ->
  room = req.params.room
  data = null
  try
    data = JSON.parse req.body.payload
  catch err
    robot.emit 'error', err

  # rest of the code here


robot.hear /midnight train/i, (res) ->
  robot.http("https://midnight-train")
    .get() (err, res, body) ->
      if err
        res.reply "Had problems taking the midnight train"
        robot.emit 'error', err, res
        return
      # rest of code here
```

对于第二个例子，值得思考的是用户会看到哪些消息。如果您有回复用户的错误处理程序，您可能不需要添加自定义消息，并且可以发回提供给 get () 请求的错误消息，当然这取决于您想多么的公开您的错误报告。

### 脚本文档

Hubot 脚本可以用在其文件顶部的注释编写文档，例如：

```
# Description:
#   <description of the scripts functionality>
#
# Dependencies:
#   "<module name>": "<module version>"
#
# Configuration:
#   LIST_OF_ENV_VARS_TO_SET
#
# Commands:
#   hubot <trigger> - <what the respond trigger does>
#   <trigger> - <what the hear trigger does>
#
# Notes:
#   <optional notes required for the script>
#
# Author:
#   <github username of the original script author>
```

这最重要的和用户面向的是 `Commands`。在加载时间，Hubot 查看每个脚本的命令部分，并建立所有命令的列表。包含的 `help.coffee` 允许用户在所有命令或搜索中寻求帮助。因此，为这些命令制作文档使用户更容易发现这些命令。

为命令制作文档的时候，这里有一些最佳实践：

- 保持在一行。帮助命令会被排序，因此第二行可能会被插到一个出乎意料的的地方，使得它没有意义。
- 将 Hubot 命名为 hubot，即使您的 hubot 被命名为其它。它将被自动替换为正确的名字。这使得无需更新文档即可轻松共享脚本。
- 对于 `robot.respond` 文档，总是以 `hubot` 作为前缀。Hubot 将自动用您的机器人名字或者机器人的别名替换。
- 查看 man 手册文档是怎样制作自己的文档的。特别是，括号表示可选项，”…” 表示任意数量的参数，等。

文档的其它部分与 bot 的开发人员更相关，尤其是依赖、配置变量和说明。对 [hubot-scripts](https://github.com/github/hubot-scripts) 的所有贡献应包含与脚本启动和运行相关的所有这些部分。

### 持久化

Hubot 暴露 `robot.brain` 内存键值存储，可用于通过脚本存储和检索数据。

```
robot.respond /have a soda/i, (res) ->
  # Get number of sodas had (coerced to a number).
  sodasHad = robot.brain.get('totalSodas') * 1 or 0

  if sodasHad > 4
    res.reply "I'm too fizzy.."

  else
    res.reply 'Sure!'

    robot.brain.set 'totalSodas', sodasHad+1
robot.respond /sleep it off/i, (res) ->
  robot.brain.set 'totalSodas', 0
  msg.reply 'zzzzz'
```

如果脚本需要检索用户数据，`robot.brain` 上有些方法通过 id，name，或者 name 的模糊匹配：`userForName`, `userForId`, `userForFuzzyName` 和 `usersForFuzzyName` 去查找一个或者多个用户。

```
module.exports = (robot) ->

  robot.respond /who is @?([\w .\-]+)\?*$/i, (res) ->
    name = res.match[1].trim()

    users = robot.brain.usersForFuzzyName(name)
    if users.length is 1
      user = users[0]
      # Do something interesting here..

      res.send "#{name} is user - #{user}"
```

### 脚本加载

加载脚本有三个主要来源：

- `scripts/` 目录下的所有脚本都与您的 hubot 绑定。
- `hubot-scripts.json` 中指定的社区脚本，并发布在 `hubot-scripts` npm 包。
- 脚本从外部 npm 包加载， 并在 `external-scripts.json` 中指定。

从 `scripts/` 目录中加载的脚本按字母顺序加载，因此您可以预期脚本的一致加载顺序。例如：

- `scripts/1-first.coffee`
- `scripts/_second.coffee`
- `scripts/third.coffee`

### 分享脚本

一旦您构建了一些新的脚本，以扩大您的机器人朋友的能力，您应该考虑与世界分享他们！至少，您需要打包脚本并将其提交到 `Node.js 程序包仓库`。您还应查看以下共享脚本的最佳实践。

#### 查看脚本是否已存在

首先[检查像您这样的脚本的 NPM 包是否已经存在](https://hubot.github.com/docs/#scripts)。如果您没有看到可以贡献的现有包，则可以轻松地开始使用 hubot 脚本 [yeoman](http://yeoman.io/) 生成器。

#### 创建一个脚本程序包

为 hubot 创建脚本包非常简单。首先安装 [yeoman](http://yeoman.io/) 生成器：

```
$ npm install -g yo generator-hubot
```

安装 hubot 生成器后，创建 hubot 脚本类似于创建一个新的 hubot。您为您的 hubot 脚本创建目录，并在其中生成一个新的 `hubot:script`。例如，如果我们想创建一个 hubot 脚本叫 “my-awesome-script”:

```
$ mkdir hubot-my-awesome-script
$ cd hubot-my-awesome-script
$ yo hubot:script
```

此时，您会被问到一些有关脚本作者的问题，脚本的名字（由目录名猜测），一个简短的描述，和去查找它的关键字（我们建议至少有 `hubot`，`hubot-scripts`）。

如果您使用的是 git，生成的目录包含一个 `.gitignore`，因此您可以初始化并添加所有内容：

```
$ git init
$ git add .
$ git commit -m "Initial commit"
```

您现在有一个准备好去启动的 hubot 脚本库。轻松打开预创建的 `src/awesome-script.coffee` 文件，并开始构建您的脚本！准备好后，您可以[按照他们的文档](https://docs.npmjs.com/getting-started/publishing-npm-packages)将其发布到 [npmjs](http://npmjs.org/)！

您可能需要为新脚本编写一些单元测试。测试脚本样例写在 `test/awesome-script-test.coffee`，您可以用 `grunt` 运行。有关测试的更多信息，请参阅[测试 Hubot 脚本](https://hubot.github.com/docs/scripting/#testing-hubot-scripts)部分。

### 监听器元数据

除了正则表达和回调，`hear` 和 `respond` 函数还接受一个可选的选项对象，它可以被用于附加任意元数据到生成的监听器对象。此元数据允许在不修改脚本包的情况下轻松扩展脚本的行为。

最重要和最常用的元数据键是 `id`。每个监听器应该被赋予一个唯一的名字（options.id，默认是 `null`）。名称应按模块划分（e.g. ‘my-module.my-listener’）。这些名称允许其他脚本直接针对单个监听器，并扩展它们的其他功能，如授权和速率限制。

其他扩展可以定义和处理其他元数据键。更多信息，请参阅 [Listener Middleware section](https://hubot.github.com/docs/scripting/#listener-middleware) 部分。

回到到较早的示例：

```
module.exports = (robot) ->
  robot.respond /annoy me/, id:'annoyance.start', (msg)
    # code to annoy someone

  robot.respond /unannoy me/, id:'annoyance.stop', (msg)
    # code to stop annoying someone
```

这些范围标识符允许您外部指定新行为，如：

- 授权政策： “允许 `annoyers` 组中的每个人执行 `annoyance.*` 命令”
- 限速：“只允许 30 分钟执行一次 `annoyance.start`”

### 中间件

有三种中间件： Receive, Listener and Response。

在监听器被查看之前，Receive 中间件执行一次。Listener 中间件在监听器匹配到信息后执行。Response 中间件在每次响应一个消息时运行。

#### 执行过程和 API

类似于 [Express middleware](http://expressjs.com/api.html#middleware)，hubot 按定义顺序执行中间件。每个中间件可以继续链（通过调用 `next`）或中断链（通过调用 `done`）。如果所有中间件继续，监听器回调被执行，并且 `done` 会被调用。中间件可以包装 `done` 的回调，以便在过程的后半部分执行代码（在执行了监听器回调或更深的中间件中断之后）。

中间件被调用：

- ```
  context
  ```

  - 查看每个中间件类型的 API，查看上下文暴露的内容。

- ```
  next
  ```

  - 一个没有额外的属性的函数，它应该被调用从而继续到下一块中间件 / 执行监听器的回调
  - `next` 应该用一个单独的、可选的参数调用：要么提供 `done` 函数，要么一个最终调用的时候叫 `done` 的新函数。如果参数没有指定，那么将假定为 `done`。

- ```
  done
  ```

  - 一个没有附加属性的函数，用于在中断中间件执行时调用，并开始执行完成功能的链。
  - `done` 在调用时无需任何参数。

每个中间件接收相同的 `context`、`next` 和 `done` 的 API 签名。不同种类的中间件在 `context` 对象中可能接收到不同的信息。详细信息，请参阅每种类型的中间件的 API。

#### 错误处理

对于同步中间件（从不产生事件循环），hubot 会自动捕获错误并发出 `error` 事件，就像标准的监听器一样。Hubot 还将自动调用最近 `done` 的回调以解除中间件堆栈。异步中间件应补捕获自己的异常，发出 `error` 事件，并调用 `done`。任何未捕获的异常都会中断所有中间件完成回调的执行。

### Listener 中间件

Listener 中间件在匹配消息的监听器和监听器执行之间插入逻辑。这允许您创建为每个匹配脚本运行的扩展。示例包括集中授权策略、限速、记录和指标。中间件像其他 hubot 脚本一样实现：中间件不是使用 `hear` 和 `respond` 方法，而是通过 `listenerMiddleware` 注册。

#### Listener 中间件示例

在 [hubot-rate-limit](https://github.com/michaelansel/hubot-rate-limit/blob/master/src/rate-limit.coffee) 中可以找到一个功能齐全的例子。

一个中间件 logging 命令执行的简单示例：

```
module.exports = (robot) ->
  robot.listenerMiddleware (context, next, done) ->
    # Log commands
    robot.logger.info "#{context.response.message.user.name} asked me to #{context.response.message.text}"
    # Continue executing middleware
    next()
```

在此示例，将为与监听器匹配的每个聊天消息写入日志消息。

做出限速决策的更复杂示例：

```
module.exports = (robot) ->
  # Map of listener ID to last time it was executed
  lastExecutedTime = {}

  robot.listenerMiddleware (context, next, done) ->
    try
      # Default to 1s unless listener provides a different minimum period
      minPeriodMs = context.listener.options?.rateLimits?.minPeriodMs? or 1000

      # See if command has been executed recently
      if lastExecutedTime.hasOwnProperty(context.listener.options.id) and
         lastExecutedTime[context.listener.options.id] > Date.now() - minPeriodMs
        # Command is being executed too quickly!
        done()
      else
        next ->
          lastExecutedTime[context.listener.options.id] = Date.now()
          done()
    catch err
      robot.emit('error', err, context.response)
```

在这个例子中，中间件检查监听器是否在过去 1000ms 中执行过。如果有，中间件立马调用 `done`，防止监听器回调被调用。如果监听器允许被执行，中间件附加一个 `done` 处理器，以便它可以记录监听器完成执行的时间。

此示例还显示了如何利用特定于监听器的元数据创建非常强大的扩展：脚本开发人员只需添加中间件并设置监听器选项，即可使用速率限制中间件去以不同速率轻松率限制命令。

```
module.exports = (robot) ->
  robot.hear /hello/, id: 'my-hello', rateLimits: {minPeriodMs: 10000}, (msg) ->
    # This will execute no faster than once every ten seconds
    msg.reply 'Why, hello there!'
```

#### Listener 中间件 API

Listener 中间件回调接收三个参数，`context`、`next`、`done`。有关 `next` 和 `done` 的描述，查看[中间件 API](https://hubot.github.com/docs/scripting/#execution-process-and-api)。Listener 中间件上下文包含这些字段：

- ```
  listener
  ```

  - `options`：定义监听器时设置的一个简单的包含选项的对象。查看[监听器元数据](https://hubot.github.com/docs/scripting/#listener-metadata)。
  - 所有其他属性应视为内部属性。

- ```
  response
  ```

  - 标准 response API 的所有部分都包含在中间件 API 中。查看[发送和回复](https://hubot.github.com/docs/scripting/#send--reply)。
  - 中间件可以用其他信息装饰（但不修改）响应对象（e.g. 例如将携带用户的 LDAP 组的属性添加到 `response.message.user`）
  - 注意：文本消息（`response.message.text`）在监听器中间件中应该被认为是不可变的。

### Receive 中间件

在任何监听器之行之前 Receive 中间件运行。它适用于尚未更新的黑名单命令以添加 ID、指标等。

#### Receive 中间件示例

这个简单的中间件禁止特定用户使用 hubot，包括 `hear` 监听器。如果用户尝试明确地运行命令，它将返回错误消息。

```
BLACKLISTED_USERS = [
  '12345' # Restrict access for a user ID for a contractor
]

robot.receiveMiddleware (context, next, done) ->
  if context.response.message.user.id in BLACKLISTED_USERS
    # Don't process this message further.
    context.response.message.finish()

    # If the message starts with 'hubot' or the alias pattern, this user was
    # explicitly trying to run a command, so respond with an error message.
    if context.response.message.text?.match(robot.respondPattern(''))
      context.response.reply "I'm sorry @#{context.response.message.user.name}, but I'm configured to ignore your commands."

    # Don't process further middleware.
    done()
  else
    next(done)
```

#### Receive 中间件 API

Receive 中间件回调接收三个参数，`context`、`next`、`done`。有关 `next` 和 `done` 的描述，请参阅[中间件 API](https://hubot.github.com/docs/scripting/#execution-process-and-api)。Receive 中间件上下文包括这些字段：

- ```
  response
  ```

  - response 对象没有 `match` 对象，因为还没有监听器运行。
  - 中间件可能通过附加信息（e.g. 添加一个携带用户的 LDAP 组的属性到 `response.message.user` ）来修饰 response 对象。
  - 中间件可能修改 `response.message` 对象。

### Response 中间件

Response 中间件与 hubot 发送到聊天室的每个消息背道而驰。它有助于消息格式化、防止密码泄露、度量等。

#### Response 中间件示例

此简单示例将发送到聊天室的链接格式从 markdown 链接（如[示例](https://example.com/)）更改为 [Slack](https://slack.com/) 支持的格式，[https://example.com | 示例。](https://example.xn--com|-gw9ir95z./)

```
module.exports = (robot) ->
  robot.responseMiddleware (context, next, done) ->
    return unless context.plaintext?
    context.strings = (string.replace(/\[([^\[\]]*?)\]\((https?:\/\/.*?)\)/, "<$2|$1>") for string in context.strings)
    next()
```

#### Response 中间件 API

Response 中间件回调接收三个参数，`context`、`next`、`done`。请参阅中间件 API 以了解 `next` 和 `done`。Receive 中间件的上下文包括这些字段：

- ```
  response
  ```

  - 此响应对象可用于从中间件发送新消息。在这些新响应中，将调用中间件。小心不要创建无限循环。

- ```
  strings
  ```

  - 一个字符串数组被发送到聊天室适配器。您可以编辑这些，或者使用 `context.strings = ["new strings"]` 去替代它们。

- ```
  method
  ```

  - 表示监听器发送的响应消息类型的字符串，例如 `send`、`reply`、`emot` 或者 `topic`。

- ```
  plaintext
  ```

  - `true` 或者 `undefined`。这将被设置为 `true`，如果消息是正常的纯文本类型，例如 `send` 和 `reply`。此属性应被视为仅读。

### 测试 hubot 脚本

[hubot-test-helper](https://github.com/mtsmfm/hubot-test-helper) 是一个用来单元测试 hubot 脚本的好框架。（请注意，为了使用 `hubot-test-helper,`，您需要一个支持 Promises 的最近 Node 版本。）

在 Hubot 实例中安装包：

```
$ npm install hubot-test-helper --save-dev
```

您还需要安装：

- 一个 JavaScript 测试框架，例如 *Mocha*
- 一个断言库，例如 *chai* 或者 *expect.js*

您或许还想安装：

- *coffee-script*（如果您用 CoffeeScript 写您的测试而不是 JavaScript）
- 一个 mocking 库例如 *Sinon.js*（如果您的脚本执行网络请求或其他异步操作）

下面是一个示例脚本，测试 [Hubot 示例脚本](https://github.com/github/generator-hubot/blob/master/generators/app/templates/scripts/example.coffee)中的前几个命令。这个脚本使用 *Mocha*、*chai*、*coffeescript*，当然还有 *hubot-test-helper*。

**test/example-test.coffee**

```
Helper = require('hubot-test-helper')
chai = require 'chai'

expect = chai.expect

helper = new Helper('../scripts/example.coffee')

describe 'example script', ->
  beforeEach ->
    @room = helper.createRoom()

  afterEach ->
    @room.destroy()

  it 'doesn\'t need badgers', ->
    @room.user.say('alice', 'did someone call for a badger?').then =>
      expect(@room.messages).to.eql [
        ['alice', 'did someone call for a badger?']
        ['hubot', 'Badgers? BADGERS? WE DON\'T NEED NO STINKIN BADGERS']
      ]

  it 'won\'t open the pod bay doors', ->
    @room.user.say('bob', '@hubot open the pod bay doors').then =>
      expect(@room.messages).to.eql [
        ['bob', '@hubot open the pod bay doors']
        ['hubot', '@bob I\'m afraid I can\'t let you do that.']
      ]

  it 'will open the dutch doors', ->
    @room.user.say('bob', '@hubot open the dutch doors').then =>
      expect(@room.messages).to.eql [
        ['bob', '@hubot open the dutch doors']
        ['hubot', '@bob Opening dutch doors']
      ]
```

**输出示例**

```
$ mocha --compilers "coffee:coffee-script/register" test/*.coffee


  example script
    ✓ doesn't need badgers
    ✓ won't open the pod bay doors
    ✓ will open the dutch doors


  3 passing (212ms)
```

## 模式

用共享模式处理常见 hubot 场景。

### 重命名 hubot 实例

当您重命名 hubot 时，他将不再回应他以前的名字。为了训练用户使用新名称，您可以选择在用户尝试说出旧名称时添加弃用通知。这里的模式逻辑是：

- 收听所有以旧名称开头的消息
- 回复用户，让他们知道新名称

设置这个非常简单：

1. 在您的 hubot 实例的 `scripts/` 目录下创建一个[捆绑脚本](https://hubot.github.com/docs/scripting/)，叫 `rename-hubot.coffee`
2. 添加以下代码，根据您的需要修改：

```
# Description:
#   Tell people hubot's new name if they use the old one
#
# Commands:
#   None
#
module.exports = (robot) ->
  robot.hear /^hubot:? (.+)/i, (res) ->
    response = "Sorry, I'm a diva and only respond to #{robot.name}"
    response += " or #{robot.alias}" if robot.alias
    res.reply response
    return
```

在上述模式中，修改 hubot 监听器和响应消息以满足您的需求。

此外，请务必注意，监听器应该基于 hubot 实际听到的内容，而不是在 hubot 适配器处理之前键入到聊天程序中的内容。例如，[HipChat Adapter](https://github.com/hipchat/hubot-hipchat) 转换 `@hubot` 为 `hubot:` 在传递给 hubot 之前。

### 弃用或重命名监听器

如果您删除脚本或更改了脚本的命令，让用户知道更改可能会有用。一种方式是在聊天中告诉他们，或者让他们通过尝试使用不再存在的命令来发现变化。另一种方式是使 Hubot 让人们知道他们何时使用了不再工作的命令。

此模式类似于上述重命名 Hubot 实例模式：

- 收听与旧命令匹配的所有消息
- 回复用户，让他们知道，它已被弃用

设置如下：

1. 在 hubot 实例的 `scripts/` 目录下创建一个叫 `deprecations.coffee` 的[捆绑脚本](https://hubot.github.com/docs/scripting/)

2. 复制一切旧的命令监听器，并将其添加到该文件中。例如，如果您出于某种糊涂的原因重新命名帮助命令：

   ```
   # Description:
   #   Tell users when they have used commands that are deprecated or renamed
   #
   # Commands:
   #   None
   #
   module.exports = (robot) ->
     robot.respond /help\s*(.*)?$/i, (res) ->
       res.reply "That means nothing to me anymore. Perhaps you meant `docs` instead?"
       return
   ```

### 通过代理转发所有 HTTP 请求

在许多企业环境中，访问互联网和（或）受保护的资源需要网络代理。对于一次性的控制，可以指定一个 [Agent](https://nodejs.org/api/http.html) 与 `robot.http` 一起使用。但是，这需要修改您的机器人用于指向代理的每个脚本。相反，您可以在全局层次指定代理，并默认使所有 HTTP 请求使用代理。

由于 node.js 处理 HTTP 和 HTTPS 请求的方式，您需要为每一种协议指定一个不同的 Agent。ScopedHTTPClient 将自动的选择正确的 ProxyAgent 为每种请求。

1. 安装 ProxyAgent。`npm install proxy-agent`
2. 在您的 hubot 实例的 `scripts/` 目录下创建一个[捆绑脚本](https://hubot.github.com/docs/scripting/)
3. 添加后面的代码，并按需修改：

```
proxy = require 'proxy-agent'
module.exports = (robot) ->
  robot.globalHttpOptions.httpAgent  = proxy('http://my-proxy-server.internal', false)
  robot.globalHttpOptions.httpsAgent = proxy('http://my-proxy-server.internal', true)
```

### 消息的动态匹配

在某些情况下，您希望动态匹配不同的消息（e.g. factoids, JIRA projects）。而不是定义一个过于宽泛的总是匹配的正则表达，您可以告诉 hubot 只有在满足某些条件时才匹配。

在一个简单的机器人中，这与仅仅把条件放在监听器回调中没什么不同，但是在处理中间件时却有很大的不同：用基本的模型，正则表达式每次匹配中间件将执行。用这个动态匹配模型，中间件仅当动态条件匹配时执行。

例如，[factoid lookup command](https://github.com/github/hubot-scripts/blob/bd810f99f9394818a9dcc2ea3729427e4101b96d/src/scripts/factoid.coffee#L95-L99) 可以被重新实现为：

```
module.exports = (robot) ->
  # Dynamically populated list of factoids
  facts =
    fact1: 'stuff'
    fact2: 'other stuff'

  robot.listen(
    # Matcher
    (message) ->
      match = message.match(/^~(.*)$/)
      # Only match if there is a matching factoid
      if match and match[1] in facts
        match[1]
      else
        false
    # Callback
    (response) ->
      fact = response.match
      res.reply "#{fact} is #{facts[fact]}"
  )
```

### 限制命令访问

Hubot 的一个了不起的特色是它能够通过一条聊天消息改变生产环境。但是，并非每个访问您的聊天服务的人都能够触发生产环境改变。

有多种不同的模式来限制访问，您可以根据您的需求来选择：

- 两个访问 buckets：完整的和用黑白名单限制的
- 为每个命令定制访问规则（基于角色的权限控制）
- 特定房间的黑白名单命令

#### 简单的每个监听器访问

在某些组织中，几乎所有员工都获得了相同级别的访问权限，只有少数员工需要受到限制（e.g. 新员工，外包员工等）。在这种模式下，您划分所有监听器集合，将 “权力命令（power commands）” 与 “常规命令（normal commands）” 分开。

一旦您将监听器分开了，您需要围绕黑白名单用户和监听器做出一些权衡。

选择用户白名单模式与用户黑名单模式的关键因素是：每个类别中的用户数量、任一类别的变化频率以及您的组织愿意接受的安全风险级别。

- 白名单用户模式（用户 X、Y、Z 有权访问权力命令；所有的其他用户只能访问常规命令）是一种更安全的访问方法（新用户没有默认的权限去执行权力命令），但有更高的维护成本（您需要将每个新用户添加到” 审批” 列表中）
- 黑名单用户模式（所有的用户都可以执行权力命令，除了用户 X、Y、Z 只能访问常规命令）是一种不太安全的方法（新的用户默认有权执行权力命令除非他们被加入了黑名单），却有较低的维护成本，在黑名单是小或者很少改变的情况下。

决定使用选择性的允许监听器和限制监听器的关键因素是：在每个分类中监听器的数量、内部和外部脚本的比例以及您的组织愿意接受的安全风险级别。

- 选择性的允许监听器（所有的监听器都是权力命令，除了监听器 A、B、C 被视为常规命令）是更安全的（新的监听器默认被限制），但维护成本却高得多（每个愚蠢 / 有趣的监听器需要被明确的降级到 “常规” 状态）。
- 选择性的限制监听器（监听器 A、B、C 是权力命令，其它的都是常规命令）是安全性更差的一种方式（新的监听器默认被放进常规分类，它能够被意想不到的访问；外部脚本在这里特别可怕），不过有更低的维护成本（无需修改 / 列举访问策略中的所有 fun/culture 脚本）。

作为额外的考虑，大多数脚本目前没有监听器 ID，因此您可能需要打开您使用的外部脚本的 PRs（或 fork）来添加监听器 ID。实际修改很容易，但与许多维护人员协调可能很费时。

一旦您决定遵循这四种模型的某一种，您需要构建适当的用户和监听器列表，以插入到您的授权中间件。

例如：用白名单用户模式去选择性的限制权力命令。

```
POWER_COMMANDS = [
  'deploy.web' # String that matches the listener ID
]

POWER_USERS = [
  'jdoe' # String that matches the user ID set by the adapter
]

module.exports = (robot) ->
  robot.listenerMiddleware (context, next, done) ->
    if context.listener.options.id in POWER_COMMANDS
      if context.response.message.user.id in POWER_USERS
        # User is allowed access to this command
        next()
      else
        # Restricted command, but user isn't in whitelist
        context.response.reply "I'm sorry, @#{context.response.message.user.name}, but you don't have access to do that."
        done()
    else
      # This is not a restricted command; allow everyone
      next()
```

记住所有匹配给定消息的监听器（包括 `robot.hear /.+/`）执行的中间件，在对监听器进行分类时，请确保包含它们。

#### 每个监听器特定的访问规则

对于较大的组织，访问的二元分类通常是不够的，需要更复杂的访问规则。

访问策略示例：

- 每个开发团队都有权编译处于某个状态的代码 (cut releases) 并部署其服务
- 运营组有权部署所有服务（但是不能 cut releases）
- 前台无法 cut releases 或部署服务

像这样的复杂政策目前最好直接在代码中实现，尽管这是[正在进行中的工作](https://github.com/michaelansel/hubot-rbac)为访问管理建立一个通用的框架。

#### 每个房间特定的访问规则

拥有多个不同用途的聊天室的组织通常希望能够使用相同的 hubot 实例，但每个房间允许使用不同的命令集。

广泛的黑名单的解决方案[正在进行中](https://github.com/kristenmills/hubot-command-blacklist)。白名单的解决方案可以采取类似的方法。

## 适配器

适配器是您希望 hubot 所运行的服务的界面。

Hubot 包含两个官方的适配器：

- [Shell](https://hubot.github.com/docs/adapters/shell/)，用于开发
- [Campfire](https://hubot.github.com/docs/adapters/campfire/)

对于大多数聊天服务，有许多第三方适配器。这是其中最受欢迎的一些：

- [Gitter](https://github.com/huafu/hubot-gitter2)
- [HipChat](https://github.com/hipchat/hubot-hipchat)
- [IRC](https://github.com/nandub/hubot-irc)
- [Rocket.Chat](https://github.com/RocketChat/hubot-rocketchat)
- [Slack](https://github.com/slackhq/hubot-slack)
- [XMPP](https://github.com/markstory/hubot-xmpp)

浏览所有的仓库通过[在 Github 上的 `hubot-adapter` topic](https://github.com/search?q=topic:hubot-adapter&type=Repositories) 或者[在 NPM 上搜索适配器](https://www.npmjs.com/search?q=hubot adapter&ranking=popularity)。添加 `hubot-adapter` [topic](https://help.github.com/articles/classifying-your-repository-with-topics/) 到您 Github 上的个人仓库，以将其包含在此列表中。

### 编写您自己的适配器

有兴趣添加自己的适配器？查看我们用于[开发适配器](https://hubot.github.com/docs/adapters/development/)的文档。

### Campfire 适配器

[Campfire](http://campfirenow.com/) 是一个由 [37signals](http://37signals.com/) 构建的基于 web 的聊天应用。Campfire 是 hubot 的一个原装适配器。

#### 起步

您将需要一个 Campfire 帐户来开始，你可以[免费注册](https://signup.37signals.com/campfire/free/signup/new)。

下一步，你需要为你的 hubot 在 Campfire 账户上创建一个用户，然后给它访问权限，以便它可以加入您的房间。如果您尚未创建房间，则需要创建一个房间。

Hubot 默认使用它的 [shell](https://hubot.github.com/docs/adapters/shell/)，用 Campfire 替代，你需要带上 `-a campfire` 运行 hubot：

```
$ bin/hubot -a campfire
```

如果你部署到 Heroku 或者使用 foreman，你需要确保在 `Procfile` 中携带 `-a campfire` 被调用：

```
web: bin/hubot -a campfire -n Hubot
```

#### 配置

适配器需要以下环境变量：

- `HUBOT_CAMPFIRE_ACCOUNT`
- `HUBOT_CAMPFIRE_TOKEN`
- `HUBOT_CAMPFIRE_ROOMS`

##### Campfire API 令牌

这可以通过登录您的 hubot 的帐户，点击 `My Info` 链接来找到， 并记下 API 令牌。

##### Campfire 房间号

如果你加入了你希望你的 hubot 加入的房间，你可以看见在 URL 中的房间的数字 ID。记下你想你的 hubot 加入的每个房间 ID。

##### Campfire 账户

这只是您访问 Campfire 帐户的域名的第一部分。例如，如果您的 Campfire 是 `hubot.campfirenow.com`，你的二级域名是 `hubot`。记下这个二级域名。

##### 在 Heroku 上配置环境变量

```
$ heroku config:set HUBOT_CAMPFIRE_TOKEN="..."

$ heroku config:set HUBOT_CAMPFIRE_ROOMS="123,321"

$ heroku config:set HUBOT_CAMPFIRE_ACCOUNT="..."
```

##### 在 Unix 上配置环境变量

```
$ export HUBOT_CAMPFIRE_TOKEN="..."

$ export HUBOT_CAMPFIRE_ROOMS="123,321"

$ export HUBOT_CAMPFIRE_ACCOUNT="..."
```

##### 在 Windows 上配置环境变量

使用 PowerShell：

```
setx HUBOT_CAMPFIRE_TOKEN "..." /m

setx HUBOT_CAMPFIRE_ROOMS "123,321" /m

setx HUBOT_CAMPFIRE_ACCOUNT "..." /m
```

### Shell 适配器

Shell 适配器提供一个简单的读取打印循环，用于与本地 hubot 进行交互。它可用于在 hubot 上使用脚本之前测试脚本。

#### 起步

要使用外壳适配器，您可以在运行 hubot 时简单地省略 -a 选项，因为它默认会使用 Shell 适配器。

```
$ bin/hubot
```

#### 配置

这个适配器不需要任何配置。

它支持两个环境变量以支持作为不同的用户测试脚本：

- HUBOT_SHELL_USER_ID: default is 1
- HUBOT_SHELL_USER_NAME: default is Shell

### 开发适配器

#### 适配器基础

所有适配器继承自在 `src/adapter.coffee` 文件中的适配器类。有明确的你想去重写的方法。下面是扩展适配器类的基本桩代码：

```
class Sample extends Adapter

  constructor: ->
    super
    @robot.logger.info "Constructor"

  send: (envelope, strings...) ->
    @robot.logger.info "Send"

  reply: (envelope, strings...) ->
    @robot.logger.info "Reply"

  run: ->
    @robot.logger.info "Run"
    @emit "connected"
    user = new User 1001, name: 'Sample User'
    message = new TextMessage user, 'Some Sample Message', 'MSG-001'
    @robot.receive message


exports.use = (robot) ->
  new Sample robot
```

#### 配置您的开发环境

1. 为您的适配器

    

   ```
   hubot-sample
   ```

    

   创建一个新的文件夹。

   - `mkdir hubot-sample`

2. 切换您的工作路径到

    

   ```
   hubot-sample
   ```

   - `cd hubot-sample`

3. 运行

    

   ```
   npm init
   ```

    

   去创建您的 package.json

   - 确保入口是 `src/sample.coffee`

4. 添加您的`.gitignore` 去包含 `node_modules`。

5. 编辑 `src/sample.coffee` 文件为您的适配器去包含上述的桩代码。

6. 编辑

    

   ```
   package.json
   ```

    

   去为 hubot 添加 peer 依赖。

   ```
   "dependencies": {
   },
   "peerDependencies": {
     "hubot": ">=2.0"
   },
   "devDependencies": {
     "coffee-script": ">=1.2.0"
   }
   ```

7. 用 `yo hubot`[命令](https://hubot.github.com/docs/)生成您的 hubot。

8. 切换工作目录到 `hubot` 你在第七步创建的。

9. 现在执行

    

   ```
   npm link
   ```

    

   去添加您的适配器到

    

   ```
   hubot
   ```

   - `npm link ../hubot-sample`

10. 运行 `hubot -a sample`。

#### Gochas

在 node 社区有一个开放的议题围绕 [npm 链接的 peer 依赖不工作](https://github.com/npm/npm/issues/5875)。要让我们的项目工作，您的代码需要做一些小的修改。

1. 引入您的

    

   ```
   hubot-sample
   ```

    

   适配器，添加下面的代码：

   ```
   try
     {Robot,Adapter,TextMessage,User} = require 'hubot'
   catch
     prequire = require('parent-require')
     {Robot,Adapter,TextMessage,User} = prequire 'hubot'
   ```

2. 在您的

    

   ```
   hubot-sample
   ```

    

   文件夹，修改

    

   ```
   package.json
   ```

    

   去包含下面的依赖以至于这种自定义导入机制将起作用

   ```
   "dependencies": {
     "parent-require": "^1.0.0"
   }
   ```

3. 现在尝试再运行 `hubot -a sample` 并看到引入正常加载了。

4. 一旦这工作正常，您可以根据您认为合适的情况构建适配器的功能。看看其他一些适配器，获取一些实现想法。

   - 一旦通过 `npm` 进行打包和部署，您就不再需要在 `hubot` 中的依赖性了，因为 peer 依赖应该作为官方模块工作。

## 部署

### 部署到 Heroku

如果你一直跟着[官方文档](https://hubot.github.com/docs/)到了这里，是时候部署了，这样你就不仅在你的本地机器能使用它了。[Heroku](http://www.heroku.com/) 是部署 hubot 的最简单和支持的方式。

安装 [Heroku Toolbelt](https://toolbelt.heroku.com/) 去开始，然后跟着他们的 “Getting Started” 操作指南，包括第一次登录：

```
$ heroku login
Enter your Heroku credentials.
Email: youremail@example.com
Password:
Could not find an existing public key.
Would you like to generate one? [Yn]
Generating new SSH public key.
Uploading ssh public key /Users/you/.ssh/id_rsa.pub
```

在新的 hubot 目录中，请确保您创建了 git 仓库，并且您的代码已经被 commit：

```
$ git init
$ git add .
$ git commit -m "Initial commit"
```

创建一个 Heroku 应用：

```
$ heroku create
Creating rosemary-britches-123... done, stack is cedar
http://rosemary-britches-123.herokuapp.com/ | git@heroku.com:rosemary-britches-123.git
Git remote heroku added
```

在您部署应用之前，您需要配置一些环境变量，供 hubot 使用。您需要的特定变量取决于您正在使用的适配器和脚本。对于 Campfire，没有其它脚本的话，你需要去设置以下环境变量：

```
$ heroku config:set HUBOT_CAMPFIRE_ACCOUNT=yourcampfireaccount
$ heroku config:set HUBOT_CAMPFIRE_TOKEN=yourcampfiretoken
$ heroku config:set HUBOT_CAMPFIRE_ROOMS=comma,separated,list,of,rooms,to,join
```

此时，您已准备好部署并开始聊天。使用 Heroku，就是执行 git push：

```
$ git push heroku master
```

您会看到一些文本飞过，最终是成功的。此时您应该可以在您配置的聊天室，看见您的机器人。如果没有，您可以看一看日志去调试：

```
$ heroku logs
```

如果您对您的 hubot 做了一些改变，在 push 它们之前，commit 一下就好：

```
$ git commit -am "Awesome scripts OMG"
$ git push heroku master
```

一些脚本需要 redis 来运行，Heroku 提供一个叫 [Redis Cloud](https://addons.heroku.com/rediscloud) 的附加装置，它是有免费计划的。使用它：

```
$ heroku addons:create rediscloud
```

在 Heroku 上的免费的 dyno 在[不活跃 30 分钟后将休眠](https://devcenter.heroku.com/articles/dyno-sleeping)。那意味着您的 hubot 将离开聊天室并且仅重新加入当它获得流量。这是极度不方便的因为大多数交互都是通过聊天完成的，hubot 必须在线、在房间里回复消息。解决这个问题的一个临时方案是，你可以使用 [hubot-heroku-keepalive](https://github.com/hubot-scripts/hubot-heroku-keepalive) 脚本，它可以让您免费的 dyno 活跃时间提升到 18 小时每天。如果你不想你的 hubot 休眠，你可能就需要[升级到 Heroku 的 bobby 计划](https://www.heroku.com/pricing)。

### 部署到 Unix

由于 Linux 的变种很多，以及 UNIX，因此 hubot 团队很难拥有规范文档，用于安装和部署到每个版本。因此，这试图概述部署所需的内容。

有 3 个主要的东西去部署和运行 hubot：

- node 和 npm
- 在服务器上更新源代码的方法
- 启动 hubot 的方法， 在崩溃时启动它， 并在代码更新时重新启动它

#### node 和 npm

首先，您的 UNIX 服务器将需要 node 和 npm。查看 node.js 的 wiki：[通过包管理器安装 Node.js](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)，[在 GNU/Linux 和 其它 UNIX 上编译](https://github.com/joyent/node/wiki/Installation#building-on-gnulinux-and-other-unix)。

#### 在服务器上更新代码

更新 hubot 代码的最简单方法是对 hubot 的源代码进行 git checkout（您在阅读[官方文档](https://hubot.github.com/docs/)期间创建的源代码，而不是 github/hubot 仓库），只需 git pull 即可获取更改。这可能感觉不够优雅，但是刚开始的时候是有用的。

如果您有 Ruby 背景，使用 [capistrano](https://github.com/capistrano/capistrano) 会让您感到更舒服。

如果您有 [Chef](http://www.chef.io/chef/) 背景，有一个用于管理部署的[部署](https://docs.chef.io/resource_deploy.html)资源。

#### 启动、停止和重启 hubot

每个 hubot 安装有一个 `bin/hubot` 脚本来处理启动 hubot。您可以在服务器上的 git checkout 处运行此命令，但是您可能会遭遇一些问题：

- 你断开终端连接，hubot 挂掉
- hubot 因为一些原因挂掉，不会自己重新启动
- 它不会开机自启

要处理断开连接，你可以在 [screen session](http://www.gnu.org/software/screen/) 中运行 `bin/hubot`，或者使用 [nohup](http://linux.die.net/man/1/nohup)。

要处理 hubot 挂掉，和自动重启，你可以想象运行 `bin/hubot` 在一个 [bash while 循环](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO-7.html#ss7.3)。但真的，你可能想要一些进程监控工具， 如 [monit](http://mmonit.com/monit/)、[god](http://godrb.com/)、[bluepill](https://github.com/arya/bluepill)、[upstart](http://upstart.ubuntu.com/)、[runit](http://smarden.org/runit/)、 [systemd](http://freedesktop.org/wiki/Software/systemd/)。

要处理开机自启，您可以创建适合 UNIX 分发的初始化脚本。如果您正在使用上面的进程监控工具之一，请确保它在启动时启动。有关配置[示例](https://github.com/github/hubot/tree/master/examples)，请参阅示例。

#### 建议

本文档有意淡化强烈建议。不过在高层次上，强烈建议避免任何过于手动和不可复现的东西。尽可能使用操作系统的包和工具，并使用适当的部署工具来更新 hubot，以及进程管理以保持 hubot 的运行。

### 部署到 Windows

尚未完全测试 —— 你的历程可能有所不同（YMMV）

在 Windows 机器上，有四个主要的部署运行 hubot 的步骤：

- node 和 npm
- 在服务器上更新源代码的方法
- 为 hubot 设置环境变量
- 启动 hubot， 在崩溃时启动它，在代码更新时重新启动它的方法

#### node 和 npm

首先，您的 windows server 需要 node 和 npm。最佳方式是用 [chocolately](http://chocolatey.org/) 使用 [nodejs.install](http://chocolatey.org/packages/nodejs.install) 包 。我发现在系统上的 path 变量没有设置正确；确保你能通过命令行运行 node/npm。如果你需要设置 path 变量，就用 “setx PATH “% PATH%;C:\Program Files\nodejs””。

你也可以直接通过 [NodeJS](https://nodejs.org/) 安装，这可以为你设置 path 变量。

> 译者注：chocolately 和 scoop 都是 windows 下的包管理软件，译者使用的是 scoop。

#### 更新服务器上的代码

在您的本地机器或直接在服务器上，去获取您服务器上的代码，您可以跟着在[官方文档](https://hubot.github.com/docs/)上的操作指南。如果您本地构建，推送您的 hubot 到 Github 并且克隆仓库到您的服务器上。不要克隆这标准的 [github/hubot repository](http://github.com/github/hubot)，确保您用 Yo Generator 去构建您自己的 hubot。

#### 设置环境变量

您会想设置您的 hubot 环境变量，在它运行的服务器上。您可以实现这个通过打开一个有管理员权限的 PowerShell 并且键入如下：

```
[Environment]::SetEnvironmentVariable("HUBOT_ADAPTER", "Campfire", "Machine")
```

这相当于去系统菜单 -> 选择高级系统设置 -> 环境变量并且添加一个新的系统变量叫 HUBOT_ADAPTER，值为 Campfire。

#### 启动、停止和重启 hubot

每个 hubot 安装有一个 `bin/hubot` 脚本去处理 hubot 的启动。您可以在您的 hubot 的文件夹下直接执行以下命令：

```
.\bin\hubot –adapter campfire
```

不过，如果您手动调用，则存在一些问题。

- 你断开连接，hubot 也挂掉
- hubot 因为某些原因挂掉，不会重启
- hubot 不开机自启

解决这些，您可能想创建一个您将从您的 hubot 路径调用的 .ps1 文件，用您喜欢的名字。[此](https://hubot.github.com/_submodules/hubot/examples/hubot-start.ps1)`examples` 目录中有此文件的副本。它应包含以下内容：

```
Write-Host "Starting Hubot Watcher"
While (1)
{
    Write-Host "Starting Hubot"
    Start-Process powershell -ArgumentList ".\bin\hubot –adapter slack" -wait
}
```

如果您使用 .ps1 文件来运行 hubot，记得允许本地未签名的 PowerShell 脚本。在一个管理员权限的 PowerShell 窗口运行下面的命令：

```
Set-ExecutionPolicy RemoteSigned
```

如果您想用一些其它的方式去启动您的进程，您可以设置这个 .ps1 文件为一个预启动项。

#### 扩充文档

还不够完整，感谢您，[帮助贡献提交一个 pull request？](https://github.com/github/hubot/pull/new/master)

### 部署到 Azure

如果你一直跟随着[本文档](https://hubot.github.com/docs/)到现在。是时候部署了，这样您就可以在本地机器之外使用它。[Azure](http://azure.microsoft.com/) 是部署 hubot 的一种方式，是 Heroku 的替代方案。

按照 hubot 的初始说明进行操作后，您将需要通过 npm 安装 azure-cli。

```
$ npm install -g azure-cli
```

在新的 hubot 目录中，请确保您创建了 git 仓库，并且您代码已经 commit：

```
$ git init
$ git add .
$ git commit -m "Initial commit"
```

然后，为您的 hubot 创建一个 [Github repository](https://help.github.com/articles/create-a-repo/)。Azure 就可以从这个 git 仓库，而不是直接从您的开发机器拉取代码了。

```
$ git remote add origin _your GitHub repo_
$ git push -u origin master
```

一旦您拥有了您的 Github 仓库，创建一个 Azure 网站链接到您的仓库。在 Azure，创建一个网站并且选择源码控制。当它询问 “您的源码控制在哪里”，选择 Github 并且链接这个网站到您在上一步创建的 git 仓库。如果你下载过 Azure PowerShell 模块，你可以通过 PowerShell 做这些。

```
$ $creds = Get-Credential
$ New-AzureWebsite mynewhubot -github -githubrepository yourgithubaccount/yourhubotreponame -githubcredentials $creds
```

一旦您完成了这个，Azure 将在您任何时间推送代码到 Github 部署您的网站。不过您的 hubot 还不能正常运行。下一步，您需要您配置部署， 告诉 Azure 如何运行 hubot。

首先，运行下面的命令去添加 `deploy.cmd` 到您的 hubot 路径。这是 Azure 用来知道如何部署 node 应用程序的文件。

```
$ azure site deploymentscript --node
```

然后，编辑此文件并查找为您提供步骤 1、2 和 3 的部分。您将添加第 4 步：

```
:: 4. Create Hubot file with a coffee extension
copy /Y "%DEPLOYMENT_TARGET%\node_modules\hubot\bin\hubot" "%DEPLOYMENT_TARGET%\node_modules\hubot\bin\hubot.coffee"
```

现在，在 hubot 的根路径下创建一个新文件叫 `server.js`，并添加下列两行：

```
require('coffee-script/register');
module.exports = require('hubot/bin/hubot.coffee');
```

最后，您需要添加环境变量到网站去确保它能够运行。下面的配置，您也可以通过图像界面来配置，或者用 Azure PowerShell 命令行，如下所示（示例显示：适配器为 slack， 网站名称为 mynewhubot）。

```
$ $settings = New-Object Hashtable
$ $settings["HUBOT_ADAPTER"] = "Slack"
$ $settings["HUBOT_SLACK_TOKEN"] = "yourslackapikey"
$ Set-AzureWebsite -AppSettings $settings mynewhubot
```

Commit 你的改变在 git 仓库，并推送到 Github，Azure 将自动获取改变并且部署到网站。

```
$ git commit -m "Add Azure settings for hubot"
$ git push
```

Azure 提供了一个市场，您可以在其中使用 Redis Labs 提供的 Redis Cloud 使用默认的 heroku-redis-brain。或者，要添加 Azure 存储大脑，您需要创建 Azure 存储帐户和帐户密钥。然后您可以在您的 hubot 根目录中执行以下操作。

```
$ npm install hubot-azure-scripts --save
```

然后 `external-scripts.json` 中添加以下行以及其它外部脚本

```
"hubot-azure-scripts/brain/storage-blob-brain"
```

最后，在您的网站中再添加两个环境变量。您可以通过图形界面或以下 PowerShell 命令做到这一点。

```
$ $settings = New-Object Hashtable
$ $settings["HUBOT_BRAIN_AZURE_STORAGE_ACCOUNT"] = "your Azure storage account"
$ $settings["HUBOT_BRAIN_AZURE_STORAGE_ACCESS_KEY"] = "your Azure storage account key"
$ Set-AzureWebsite -AppSettings $settings mynewhubot
```

现在任何需要大脑的脚本都可以运行。您应该查找其他脚本或通过查看[文档](https://hubot.github.com/docs/scripting/)编写自己的脚本。 Hubot 的所有普通脚本都与 Azure 上的托管 hubot 兼容。

#### 故障排除提示和技巧

由于 Azure 基于 Windows，您可能会遇到路径长度问题。要解决此问题，您可以将环境变量 `IN_PLACE_DEPLOYMENT` 设置为 1，并使用[自定义部署脚本来利用 NPM3](https://github.com/felixrieseberg/azure-npm3) 和 flat 模块安装。

如果使用 Azure 的免费 tier，还可以添加部署后步骤，通过使用诸如 `startup.sh` 之类的脚本（相对于 src 目录）设置环境变量 `POST_DEPLOYMENT_ACTION` 来在启动时 ping 服务器。

启动脚本示例：

```shell
let retrys=0
while : ; do
    STATUSCODE=$(curl --silent --output /dev/stderr --write-out "%{http_code}" https://${WEBSITE_SITE_NAME}.azurewebsites.net/heroku/keepalive)
    echo $STATUSCODE
    [[ $retrys -ne 5 ]] || break
    echo $retrys
    ((retrys++))
    [[ $STATUSCODE -ne 200 ]] || break
done
```