---
title: Vue 组件复用
date: 2020-12-03 14:47:27
categories:
- 前端
- 架构
tags:
- Vue
---

组件，是一个具有一定功能，且不同组件间功能相对独立的模块。组件可以是一个按钮、一个输入框、一个视频播放器等等。

可复用组件，高内聚、低耦合。

那么，什么构成了组件呢。以浏览器的原生组件 video 为例，分析一下组件的组成部分。

```html
<video
 src="example.mp4"
 width="320"
 height="240"
 onload="loadHandler"
 onerror="errorHandler">
 Your browser does not support the video tag.
</video>
```

实例中能看出，组件由状态、事件和嵌套的片断组成。状态，是组件当前的某些数据或属性，如 video 中的 src、width 和 height。事件，是组件在特定时机触发一些操作的行为，如 video 在视频资源加载成果或失败时会触发对应的事件来执行处理。片段，指的是嵌套在组件标签中的内容，该内容会在某些条件下展现出来，如在浏览器不支持 video 标签时显示提示信息。

在 Vue 组件中，状态称为 props，事件称为 events，片段称为 slots。组件的构成部分也可以理解为组件对外的接口。良好的可复用组件应当定义一个清晰的公开接口。

- Props 允许外部环境传递数据给组件
- Events 允许组件触发外部环境的副作用
- Slots 允许外部环境将额外的内容组合在组件中。

使用 vue 对 video 组件做拓展，构造出一个支持播放列表的组件 myVideo：

```vue
<my-video
 :playlist="playlist"
 width="320"
 height="240"
 @load="loadHandler"
 @error="errorHandler"
 @playnext="nextHandler"
 @playprev="prevHandler">
 <div slot="endpage"></div>
</my-video>
```

myVideo 组件有着清晰的接口，接收播放列表、播放器宽高等状态，能够触发加载成功或失败、播放上一个或下一个的事件，并且能自定义播放结束时的尾页，可用于插入广告或显示下一个视频信息。

## 构造思想

*引用自 [https://www.jb51.net/article/109347.htm](https://www.jb51.net/article/109347.htm)*

### 组件间通信

在 Vue.js 中，父子组件的关系可以总结为 props down, events up 。父组件通过 props 向下传递数据给子组件，子组件通过 events 给父组件发送消息。

### 命名业务无关

组件的命名应该跟业务无关。应该依据组件的功能为组件命名。

例如，一个展示公司部门的列表，把每一项作为一个组件，并命名为 DepartmentItem。这时，有一个需求要展示团队人员列表，样式跟刚刚的部门列表一样。显然，DepartmentItem 这个名字就不适合了。

因此，可复用组件在命名上应避免跟业务扯上关系，以组件的角色、功能对其命名。Item、ListItem、Cell。可以参考 Bootstrap、ElementUI 等一些 UI 框架的命名。

### 业务数据无关

可复用组件只负责 UI 上的展示和一些交互以及动画，如何获取数据跟它无关，因此不要在组件内部去获取数据，以及任何与服务端打交道的操作。可复用组件只实现 UI 相关的功能。

### 组件职责

约束好组件的职责，能让组件更好地解耦，知道什么功能是组件实现的，什么功能不需要实现。

组件可以分为通用组件（可复用组件）和业务组件（一次性组件）。

可复用组件实现通用的功能（不会因组件使用的位置、场景而变化）：

- UI 的展示
- 与用户的交互（事件）
- 动画效果

业务组件实现偏业务化的功能：

- 获取数据
- 和 vuex 相关的操作
- 埋点
- 引用可复用组件

可复用组件应尽量减少对外部条件的依赖，所有与 vuex 相关的操作都不应在可复用组件中出现。

组件应当避免对其父组件的依赖，不要通过 this.$parent 来操作父组件的示例。父组件也不要通过 this.$children 来引用子组件的示例，而是通过子组件的接口与之交互。

### 命名空间

可复用组件除了定义一个清晰的公开接口外，还需要有命名空间。命名空间可以避免与浏览器保留标签和其他组件的冲突。特别是当项目引用外部 UI 组件或组件迁移到其他项目时，命名空间可以避免很多命名冲突的问题。

```vue
<xl-button></xl-button>
<xl-table></xl-table>
<xl-dialog></xl-dialog>
...
```

业务组件也可以有命令空间，跟通用组件区分开。这里用 st (section) 来代表业务组件。

```vue
<st-recommend></st-recommend>
<st-qq-movie></st-qq-movie>
<st-sohu-series></st-sohu-series>
```

### 上下文无关

还是上面那句话，可复用组件应尽量减少对外部条件的依赖。没有特别需求且单个组件不至于过重的的前提下，不要把一个有独立功能的组件拆分成若干个小组件。

```vue
<table-wrapper>   
 <table-header slot="header" :headers="exampleHeader"></table-header>   
 <table-body slot="body" :body-content="exampleContents"></table-body>   
</table-wrapper>
```

TableHeader 组件和 TableBody 组件依赖当前的上下文，即 TableWrapper 组件嵌套的环境下。你可以有更好的解决办法：

```vue
<xl-table :headers="exampleHeader" :body-content="exampleContents"></xl-table>
```

上下文无关原则能够降低组件使用的门槛。

### 数据扁平化

定义组件接口时，尽量不要将整个对象作为一个 prop 传进来。

```vue
<!-- 反例 -->
<card :item="{ title: item.name, description: item.desc, poster: item.img }></card>
```

每个 prop 应该是一个简单类型的数据。这样做有下列几点好处：

- 组件接口清晰
- props 校验方便

当服务端返回的对象中的 key 名称与组件接口不一样时，不需要重新构造一个对象

```vue
<card
 :title="item.name"
 :description="item.desc"
 :poster="item.img">
</card>
```

扁平化的 props 能让我们更直观地理解组件的接口。

### 使用自定义事件实现数据的双向绑定

有时候，对于一个状态，需要同时从组件内部和组件外部去改变它。

例如，模态框的显示和隐藏，父组件可以初始化模态框的显示，模态框组件内部的关闭按钮可以让其隐藏。一个好的办法是，使用自定义事件改变父组件中的值：

```vue
<modal :show="show" @showchange="show = argument[0]"></modal>
```

```vue
<!-- Modal.vue -->
 
<template>
 <div v-show="show">
  <h3>标题</h3>
  <p>内容</p>
  <a href="javascript:;" rel="external nofollow" rel="external nofollow" @click="close">关闭</a>
 </div>
</template>
 
<script>
 export default {
  props: {
   show: String
  },
  methods: {
   close () {
    this.$emit('input', false)
   }
  }
 }
</script>
```

用户点击关闭按钮时，Modal 组件发送一个 input 自定义事件给父组件。父组件监听到 input 事件时，把 show 设置为事件回调的第一个参数。

特别地，当状态名称为 value，事件名称为 input 时，可以使用 v-model 指令语法糖：

```vue
<modal :value="show" @input="show = argument[0]"></modal>
```

等价于

```vue
<modal v-model="show"></model>
```

要让组件的 v-model 生效，它必须：

- 接受一个 value 属性
- 在有新的 value 时触发 input 事件

注意：由于每个组件的 input 事件只能用来对一个数据进行双向绑定，所以当存在多个需要向上同步的数据时，请不要使用 v-model，请使用多个自定义事件，并在父组件中同步新的值。

```vue
<modal
 :show="show" @showchange="show = argument[0]"
 :content="content" @contentchange="content = argument[0]">
</model>
```

### 使用自定义 watcher 优化 DOM 操作

在开发中，有些逻辑无法使用数据绑定，无法避免需要对 DOM 的操作。例如，视频的播放需要同步 Video 对象的播放操作及组件内的播放状态。可以使用自定义 watcher 来优化 DOM 的操作。

```vue
<!-- MyVideo.vue -->
 
<template>
 <div>
  <video ref="video" src="src"></video>
  <a href="javascript:;" rel="external nofollow" rel="external nofollow" @click="togglePlay">{{ playing ? '暂停' : '播放' }}</a>
 </div>
</template>
 
<script>
 export default {
  props: {
   src: String // 播放地址
  },
  data () {
   return {
    playing: false // 是否正在播放
   }
  },
  watch: {
   // 播放状态变化时，执行对应操作
   playing (val) {
    let video = this.$refs.video
    if (val) {
     video.play();
    } else {
     video.pause();
    }
   }
  },
  method: {
   // 切换播放状态
   togglePlay () {
    this.playing = !this.playing
   }
  }
 }
</script>
```

示例中，自定义 watcher 在监听到 playing 状态变化时，会执行播放或暂停操作。遇到对视频播放状态的处理时，只需要关注 playing 状态即可。

### 项目骨架

单组件不异过重，组件在功能独立的前提下应该尽量简单，越简单的组件可复用性越强。当你实现组件的代码，不包括CSS，有好几百行了（这个大小视业务而定），那么就要考虑拆分成更小的组件。

当组件足够简单时，就可以在一个更大的业务组件中去自由组合这些组件，实现我们的业务功能。因此，理想情况下，组件的引用层级，只有两级。业务组件引用通用组件。

我们可以得到一个扁平化的结构。

![img](https://img.jbzj.com/file_images/article/201703/2017032409221725.jpg)

在一个庞大的项目当中，组件间的引用关系会更复杂一些。当单页应用有多个路由，每个路由组件过重，需要拆分模块时。组件结构会变成下图这样。

![img](https://img.jbzj.com/file_images/article/201703/2017032409221826.jpg)

按照这个思路构建我们的项目，最后的源代码目录结构（不包括构建流程文件）：

```
│ App.vue     # 顶级组件
│ client-entry.js # 前端入口文件
│ config.js    # 配置文件
│ main.js     # 主入口文件
│ 
├─api        # 接口 API
├─assets      # 静态资源
├─components    # 通用组件
├─directives    # 自定义指令
├─mock       # Mock 数据
├─plugins      # 自定义插件
├─router      # 路由配置
├─sections     # 业务组件
├─store       # Vuex Store
├─utils       # 工具模块
└─views       # 路由页面组件
```

在通用组件中还可以区分容器组件、布局组件和其他功能性组件等。

## Vue 组件封装打包，发布到 npm

*引用自 [https://blog.csdn.net/qq_31851435/article/details/106647833](https://blog.csdn.net/qq_31851435/article/details/106647833)*

### 使用 webpack-simple 模板

#### 1. 新建一个 vue 项目

```bash
vue init webpack-simple 项目名
```

然后再 `npm install` 安装一下依赖，`npm run dev` 测试

#### 2. 在新建项目的 src 目录下新建一个目录（components）用来存放所有自定义组件源码

```
src/components/xx-btn/xx-btn.vue
```

#### 3. 组件封装好后在组件目录（components）下新建一个 index.js 文件，为组件库的入口文件

```
src/components/index.js
```

```js
// 导入封装的组件
import xxBtn from './xx-btn/xx-btn.vue'
 
const components = {
  // 通过install来安装组件
  install(Vue) {
    Vue.component(
      'xxBtn', xxBtn
    )
  }
}
 
// 注意这里的判断，很重要
if(typeof window !== 'undefined' && window.Vue) {
  window.Vue.use(comment)
}
 
// 导出组件库
export default components
```

#### 4. 修改 package.json 文件

```json
 {
  "name": "xx-btn", // 发布的模块名称，发布线上后，可以通过npm install xx-btn来引用该模块，一般Vue组件以vue-开头
  "description": "test npm", // 组件库描述
  "version": "1.0.0", // 版本号
  "author": "xxx", // 作者
  "license": "MIT", // 代码授权许可
  "private": false, // 公开项目，因为组件包是公用的，所以private要改为false
  
  "scripts": { // 运行命令
    "dev": "cross-env NODE_ENV=development webpack-dev-server --open --hot",
    "build": "cross-env NODE_ENV=production webpack --progress --hide-modules"
  },
  
  "main": "dist/xx-btn.min.js", // 打包后的入口文件，若不配置，则在其他项目中就无法使用import xx from '包名'来引入组件，只能以包名作为起点来指定相对路径
  
  "keywords": [ // 关键词，可以通过npm搜索你填写的关键词找到你的模块
    "xx-btn",
    "components"
  ],
  
  "files": [ // 指定打包后发布到npm的文件，没指定则不传，package.json是默认上传的
    "dist",
    "src/components"
  ],
 
  "homepage": "https://xx.github.io/xx/xx-btn/dist/index.html", // 项目官网的url
  
  "bugs": { // 填写一个bug提交地址或者一个邮箱，被你的模块坑到的人可以通过这里吐槽
    "url": "https://github.com/xx/xx-btn/issues"
  },
  
  "repository": { // 指定代码所在的仓库地址
    "type": "git",
    "url": "https://github.com/xx/xx-btn.git"
  },
  
  ...
}
```

#### 5. 修改 webpack.config.js 文件

```js
// entry: "./src/main.js",
entry: "./src/components/index.js", // 入口文件
 
output: { // 修改输出文件到 dist 下
 
  path: path.resolve(__dirname, './dist'),
  publicPath: '/dist/',
  
  // filename: 'build.js'
  filename: "xx-btn.min.js", // 生成的文件名
  library: "xx-btn", // 指定的就是你使用require时的模块名
  libraryTarget: "umd", // 会生成不同umd的代码,可以只是commonjs标准的，也可以是指amd标准的，也可以只是通过script标签引入的
  umdNamedDefine: true // 会对 UMD 的构建过程中的 AMD 模块进行命名。否则就使用匿名的 define
},
```

#### 6. 编译打包，生成的文件在 dist 目录下

```bash
npm run build
```

#### 7. 测试插件

```bash
npm pack
```

npm pack 之后，会在当前目录下生成一个 xx-btn-1.0.0.tgz 的文件。

打开一个vue项目，将压缩文件放到项目某个目录下，执行命令

```bash
npm install 路径\xx-btn-1.0.0.tgz
```

路径是压缩包所在的**绝对路径**，文件名必须是压缩包的全名

安装好插件后，在项目的入口文件main.js里引入使用

```js
import xxBtn from 'xx-btn'
Vue.use(xxBtn)
```

之后就可以在项目的任意组件中引用就行了，如果引入成功，则说明打包成功，接下来就是发布到npm上了

#### 8. 发布 npm

todo