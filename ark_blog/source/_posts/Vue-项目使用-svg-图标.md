---
title: Vue 项目使用 svg 图标
date: 2022-4-14 11:38:00
categories:
- 前端
tags:
- Vue
---

svg-sprite-loader

<!-- more -->

参考 [vue-cli3使用svg图标的详细步骤](https://segmentfault.com/a/1190000038883444)

## 安装依赖

```bash
npm install svg-sprite-loader -D
```

## vue.config.js 配置

在 `vue.config.js` 里添加配置

```javascript
const path = require('path')
function resolve(dir) {
    return path.join(__dirname, dir);
}

module.exports={
    chainWebpack: config => {
        config.module.rule('svg').exclude.add(resolve('src/assets/svg')).end();
        config.module.rule('svg-sprite')
            .test(/\.svg$/)
            .include.add(resolve('src/assets/svg')).end()
            .use('svg-sprite-loader')
            .loader('svg-sprite-loader')
            .options({
                symbolId: 'icon-[name]'
            });
    }
}
```

如果有其它模块的 webpack 用到了 `src/assets/svg`，则需要先 exclude

## 创建 svg 组件

在 `src/components` 下创建文件夹 `svgIcon`，并在该文件夹下创建 `index.vue`，`index.js`

index.vue:

```vue
<template> 
    <svg :class="svgClass" aria-hidden="true">
        <use :xlink:href="'#icon-'+iconName" />
    </svg>
</template>
    
<script>    
export default {    
    name: 'svgSpriteIcon',
    props:{
        iconName:{
            type: String,
            required: true
        },
        className:{ 
            type: String,
            default: ''
        }
    },
    computed:{
        svgClass(){
            if(this.className){
                return `svg-icon ${this.className}`;
            }else{
                return 'svg-icon';
            }
        }
    }
}  
</script>

<style scoped>    
.svg-icon{
    width: 1em;
    height: 1em;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
}
</style>
```

index.js:

```javascript
import Vue from 'vue'
import svgSpriteIcon from './index.vue'

Vue.component('svg-sprite-icon', svgSpriteIcon);  //挂载全局组件
// 是导入 svg 文件夹下的所有 svg 文件
const requireAll = requireContext => requireContext.keys().map(requireContext);
const req = require.context('@assets/svg', true, /\.svg$/);
/*
   第一个参数是: '@assets/svg' => 需要检索的目录 
   第二个参数是：true => 是否检索子目录
   第三个参数是: /\.svg$/ => 匹配文件的正则
*/
requireAll(req);
```

## 导入

在 main.js 里添加

```javascript
import '@/components/svgIcon/index.js'
```

## 使用

```vue
<svg-sprite-icon :icon-name="xxx" />
```
