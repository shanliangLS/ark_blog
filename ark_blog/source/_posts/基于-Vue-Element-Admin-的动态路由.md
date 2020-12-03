---
title: 基于 Vue Element Admin 的动态路由
date: 2020-12-03 10:44:48
categories:
- 前端
- 架构
tags:
- Vue
- Vue Element Admin
- Vue Router
---

[vue element admin 官网](https://panjiachen.gitee.io/vue-element-admin-site/zh/)

## 源代码

侧边栏生成 `src/layout/components/Sidebar/index.vue`

```vue
<!-- ... -->
    <sidebar-item v-for="route in permission_routes" :key="route.path" :item="route" :base-path="route.path" />
<!-- ... -->

<script>
import { mapGetters } from 'vuex'
    
export default {
  computed: {
    ...mapGetters([
      'permission_routes', // permission_routes即为侧边栏路由数据，调用vuex获取
    ]),
    // ...
  }
}
</script>
```

侧边栏路由储存在 `src/store/modules/permission.js` 的 vuex 中

```js
import { asyncRoutes, constantRoutes } from '@/router' // constantRoutes为常驻路由项，asyncRoutes为异步路由项，均从本地获取

/**
 * Use meta.role to determine if the current user has permission
 * @param roles
 * @param route
 */
function hasPermission(roles, route) {
  if (route.meta && route.meta.roles) {
    return roles.some(role => route.meta.roles.includes(role))
  } else {
    return true
  }
}

/**
 * Filter asynchronous routing tables by recursion
 * @param routes asyncRoutes
 * @param roles
 */
export function filterAsyncRoutes(routes, roles) {
  const res = []

  routes.forEach(route => {
    const tmp = { ...route }
    if (hasPermission(roles, tmp)) {
      if (tmp.children) {
        tmp.children = filterAsyncRoutes(tmp.children, roles)
      }
      res.push(tmp)
    }
  })

  return res
}

const state = {
  routes: [],
  addRoutes: []
}

const mutations = { // 将常驻路由项和异步路由项合并
  SET_ROUTES: (state, routes) => {
    state.addRoutes = routes
    state.routes = constantRoutes.concat(routes)
  }
}

const actions = { // 根据用户权限筛选异步路由项
  generateRoutes({ commit }, roles) {
    return new Promise(resolve => {
      let accessedRoutes
      if (roles.includes('admin')) {
        accessedRoutes = asyncRoutes || []
      } else {
        accessedRoutes = filterAsyncRoutes(asyncRoutes, roles)
      }
      commit('SET_ROUTES', accessedRoutes)
      resolve(accessedRoutes)
    })
  }
}

export default {
  namespaced: true,
  state,
  mutations,
  actions
}
```

登录成功后 `src/permission.js` 首次触发路由生成

```js
import router from './router'
import store from './store'

// ...

router.beforeEach(async(to, from, next) => {
    // ...
    
    // get user info
    // note: roles must be a object array! such as: ['admin'] or ,['developer','editor']
    const { roles } = await store.dispatch('user/getInfo')

    // generate accessible routes map based on roles
    const accessRoutes = await store.dispatch('permission/generateRoutes', roles)

    // dynamically add accessible routes
    router.addRoutes(accessRoutes)

    // hack method to ensure that addRoutes is complete
    // set the replace: true, so the navigation will not leave a history record
    next({ ...to, replace: true })
    
    // ...
}
```

## 修改

### mock

`mock/role` 目录下新建 `my-routes.js` 文件，格式参考 `src/router/index.js` ，`component` 属性改为字符串

```js
const myAsyncRoutes = [
  {
    path: '/plugins',
    component: 'Layout',
    meta: { title: '插件', icon: 'eye-open' },
    children: [
      {
        path: 'PluginContainer/:src*', // 动态路由匹配，将:src处的内容转化为路由参数$route.params
        component: 'plugins/PluginContainer',
        name: 'Plugins',
        meta: { title: '插件页', icon: 'eye-open' },
        hidden: true
      },
      {
        path: 'PluginContainer/my-plugin',
        component: 'plugins/PluginContainer',
        name: 'my-plugin',
        meta: { title: 'my-plugin', icon: 'eye-open' },
      },
      {
        path: 'PluginContainer/ssr-genesis',
        component: 'plugins/PluginContainer',
        name: 'ssr-genesis',
        meta: { title: 'ssr-genesis', icon: 'eye-open' },
      }
    ]
  },
  
  /*{
    ...
  },*/

  // 404 page must be placed at the end !!!
  { path: '*', redirect: '/404', hidden: true }
]

module.exports = {
  myAsyncRoutes
}
```

`mock/role/index.js` 引用 `my-routes.js` ，在 `module.exports` 中新增接口

```js
const { myAsyncRoutes } = require('./my-routes.js')

// ...

module.exports = [
  {
    url: '/vue-element-admin/my-routes',
    type: 'get',
    response: _ => {
      return {
        code: 20000,
        data: [
          {
            name: 'my-routes',
            title: '主菜单1',
            routes: myAsyncRoutes
          },
          {
            name: 'my-components',
            title: '组件2',
            routes: componentsRouter
          },
          {
            name: 'abc',
            title: '菜单3',
            routes: []
          }
        ]
      }
    }
  },
    
  /*{
    ...
  },*/
}
```

`src/api` 目录下新建 `menu.js`

```js
import request from '@/utils/request'

export const getMyRoutes = () => {
  return request({
    url: '/vue-element-admin/my-routes',
    method: 'get'
  })
}
```

### 路由生成

修改 `src/store/modules/permission.js` ，从本地获取路由改为从接口获取路由，并增加对 `component` 属性的转换

```js
import { constantRoutes } from '@/router' // 常驻路由项依旧从本地获取
import { getMyRoutes } from '@/api/menu' // 异步路由项从接口获取
import Layout from '@/layout/index' // Layout组件

// ...

function filterAsyncRoutesAddComponent(asyncRouterMap) { // 遍历后台传来的路由字符串，转换为组件对象
  return asyncRouterMap.filter(route => {
    if (route.component) {
      // Layout组件特殊处理
      if (route.component === 'Layout') {
        route.component = Layout
      } else {
        route.component = loadView(route.component)
      }
    }
    if (route.children != null && route.children && route.children.length) {
      route.children = filterAsyncRoutesAddComponent(route.children)
    }
    return true
  })
}

export const loadView = (view) => { // 路由懒加载
  return (resolve) => require([`@/views/${view}`], resolve)
}

const state = {
  totalRoutes: [], // 所有路由数据
  routes: [],
  addRoutes: []
}

const mutations = {
  SET_TOTAL_ROUTES: (state, routes) => { // 储存从接口获取的所有路由数据
    state.totalRoutes = routes
  },
  SET_ROUTES: (state, routes) => { // 将常驻路由项和异步路由项合并
    state.addRoutes = routes
    state.routes = constantRoutes.concat(routes)
  }
}

const actions = {
  generateRoutes({ commit }, { roles, routesName = 'my-routes' }) {
    return new Promise(resolve => {
      // 向后端请求路由数据
      getMyRoutes().then(res => {
        let resolveRoutes = []
        const filteredRoutes = res.data.filter((item) => {
          if (roles.includes('admin')) { // 根据用户权限筛选异步路由项
            item.routes = item.routes || []
          } else {
            item.routes = filterAsyncRoutes(item.routes, roles)
          }
          item.routes = filterAsyncRoutesAddComponent(item.routes)
          resolveRoutes = [...resolveRoutes, ...item.routes]
          return item
        })
        const accessedRoutes = filteredRoutes.find(item => item.name === routesName).routes
        // 路由项去重
        const obj = {}
        resolveRoutes = resolveRoutes.reduce((cur, next) => {
          obj[next.path] ? '' : obj[next.path] = true && cur.push(next)
          return cur
        }, [])
        commit('SET_TOTAL_ROUTES', filteredRoutes)
        commit('SET_ROUTES', accessedRoutes)
        resolve(resolveRoutes)
      })
    })
  },
  updateSidebar({ commit }, routesName = 'my-routes') { // 切换侧边栏
    const accessedRoutes = state.totalRoutes.find(item => item.name === routesName).routes
    commit('SET_ROUTES', accessedRoutes)
  }
}

export default {
  namespaced: true,
  state,
  mutations,
  actions
}
```

将 `src/permission.js` 的 `store.dispatch('permission/generateRoutes', roles)` 中的 `roles` 改为 `{roles}`

```js
import router from './router'
import store from './store'

// ...

router.beforeEach(async(to, from, next) => {
    // ...
    
    // get user info
    // note: roles must be a object array! such as: ['admin'] or ,['developer','editor']
    const { roles } = await store.dispatch('user/getInfo')

    // generate accessible routes map based on roles
    const accessRoutes = await store.dispatch('permission/generateRoutes', { roles })

    // dynamically add accessible routes
    router.addRoutes(accessRoutes)

    // hack method to ensure that addRoutes is complete
    // set the replace: true, so the navigation will not leave a history record
    next({ ...to, replace: true })
    
    // ...
}
```

将 Vue Genesis 生成的 `client` 目录下的静态文件移动到 `public` 目录下。

在 `src/views` 目录下新建 `plugins/PluginContainer.vue` ，需安装 `npm install @fmfe/genesis-remote axios`

```vue
<template>
  <div class="plugin-container">
    <RemoteView :fetch="fetch" />
  </div>
</template>

<script>
import axios from 'axios'
import { RemoteView } from '@fmfe/genesis-remote'

export default {
  name: 'PluginContainer',
  components: {
    RemoteView
  },
  data() {
    return {
      plugin_src: undefined,
      tempRoute: {}
    }
  },
  created() {
    this.plugin_src = this.$route.params.src
    this.tempRoute = Object.assign({}, this.$route)
  },
  methods: {
    async fetch() {
      this.setTagsViewTitle() // 设置导航栏标签
      this.setPageTitle() // 设置页面标题
      const src = this.plugin_src + '/app.json'
      const res = await axios.get(src) // 获取ssr-genesis静态文件
      if (res.status === 200) {
        return res.data
      }
      return null
    },
    setTagsViewTitle() {
      const route = Object.assign({}, this.tempRoute, { title: `${this.plugin_src}` })
      this.$store.dispatch('tagsView/updateVisitedView', route)
    },
    setPageTitle() {
      document.title = `${this.plugin_src}`
    }
  }
}
</script>
```

### 侧边栏路由切换

在 `src/components` 目录下新建 `TopMenu/index.vue`（未编写 CSS 样式）

```vue
<template>
  <ul>
    <li v-for="menu in menu_arr"><a @click="updateRoutes(menu.name)">{{menu.title}}</a></li>
  </ul>
</template>

<script>
import store from '../../store'

export default {
  name: 'TopMenu',
  data() {
    return {
      menu_arr: []
    }
  },
  mounted() { // 获取存储的所有路由数据
    this.menu_arr = store.state.permission.totalRoutes
  },
  methods: {
    updateRoutes(routesName) { // 切换侧边栏路由
      store.dispatch('permission/updateSidebar', routesName)
    }
  }
}
</script>

<style lang="scss" scoped>
ul {
  padding-left: 0;

  li {
    margin-left: 8px;
    display: inline;
    float:left
  }
}
</style>
```

`src/layout/components/Navbar.vue` 中，在 `<hamburger>` 和 `<breadcrumb>` 中间插入切换侧边栏的菜单

```vue
<template>
  <div class="navbar">
    <hamburger id="hamburger-container" :is-active="sidebar.opened" class="hamburger-container" @toggleClick="toggleSideBar" />

    <top-menu class="top-menu-container" />

    <breadcrumb id="breadcrumb-container" class="breadcrumb-container" />
    <!-- ... -->
  </div>
</template>

<script>
import TopMenu from '@/components/TopMenu'
    
export default {
  components: {
    TopMenu
  }
  // ...
}
</script>

<style lang="scss" scoped>
.navbar {
  /* ... */
  .top-menu-container {
    float: left;
  }
  /* ... */
}
</style>
```
