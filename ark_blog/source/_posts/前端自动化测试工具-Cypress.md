---
title: 前端自动化测试工具 Cypress
date: 2020-12-03 10:54:45
categories:
- 快速上手
tags:
- 测试
- Tools
---

Cypress

一个端到端 (e2e) 的 web 自动化测试工具

## 安装

```bash
cd Crypress_project
npm init -y
npm install cypress --save-dev
```

修改 `package.json` 文件

```json
{
  "scripts": {
    "cypress": "cypress open"
  }
}
```

启动

```bash
npm run cypress
```

第一次启动 Cypress，它已经为我们准备了丰富的例子

{% asset_img cypress1.png %}

### 项目结构

```
cypress
├── fixtures
│   └── example.json
├── integration # 存放测试文件
│   └── examples
│       ├── actions.spec.js
│       ├── aliasing.spec.js
│       ├── assertions.spec.js
│       ├── connectors.spec.js
│       ├── cookies.spec.js
│       ├── cypress_api.spec.js
│       ├── files.spec.js
│       ├── local_storage.spec.js
│       ├── location.spec.js
│       ├── misc.spec.js
│       ├── navigation.spec.js
│       ├── network_requests.spec.js
│       ├── querying.spec.js
│       ├── spies_stubs_clocks.spec.js
│       ├── traversal.spec.js
│       ├── utilities.spec.js
│       ├── viewport.spec.js
│       ├── waiting.spec.js
│       └── window.spec.js
├── plugins # 定义一些初始化设置跟变量
│   └── index.js
└── support # 可以抽取一些公共的方法作为我们的私有命令
    ├── commands.js
    └── index.js
```

### 简单测试

在 `/intgration` 目录下新建 `sample.spec.js`

{% asset_img cypress2.png %}

```js
describe('simple test case for cypress',function(){
    it('Does not match!',function(){
        expect(true).to.equal(true)
    })
})
```

Cypress 界面中出现该测试用例，点击即可运行

{% asset_img cypress3.png %}

尽管我们没有做一些实际的事情，但是这个测试是通过的

{% asset_img cypress4.png %}

## 实际测试用例

Cypress 官方测试网站 [https://example.cypress.io/commands/actions](https://example.cypress.io/commands/actions)

### 访问百度首页并搜索 `cypress test`

```
describe('My First Test', function () {
    it('Does not do much!', function () {
        cy.visit("https://www.baidu.com")

        cy.get("#kw", {timeout: 2000}).type("cypress test")
       
        cy.wait(100)
       
        cy.get("#su", { timeout: 2000 }).click()
    })
})
```

Cypress 运行结果

{% asset_img cypress5.png %}

## Cypress 语法

记一些 Cypress 的语法，有时间再加

### 查找元素

如果你以前使用过 jQuery ，则可能习惯于查询以下元素

```js
$('.my-selector')
```

在 Cypress 中，查询元素相同

```js
cy.get('.my-selector')
```

实际上， Cypress 捆绑了 jQuery ，并公开了许多 DOM 遍历方法，因此你可以使用已经熟悉的API轻松处理复杂的HTML结构

```
// Each method is equivalent to its jQuery counterpart. Use what you know!
cy.get('#main-content')
  .find('.article')
  .children('img[src^="/static"]')
  .first()
```

#### 元素定位方式

- get：按 css 或元素特定属性的方式定位元素
- contains：按特定字符串定位元素