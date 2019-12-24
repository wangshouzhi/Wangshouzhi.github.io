---
layout:     post
title:      vue项目实现动态路由功能
subtitle:   使用AddRouter方法实现动态路由，实现从后台获取路由数据
date:       2019-05-14
author:     Wsz
header-img: img/adad.jpg
catalog: true
tags:
    - vue
    - Addrouter
    - 动态路由加载
---

## 项目需求

   需要实现导航菜单从后台拉取的效果；根据登录用户的权限不同分别拉出来的导航菜单也不一样，另外可操作的界面也存在区别。

## 思路

在vue-router对象中首先初始化公共路由，比如（404，login）等，然后在用户登陆成功，根据用户的角色信息，获取对应权限菜单信息menuList，并将后台返回的menuList转换成我们需要的router数据结构，然后通过vue-router2.2新添的router.addRouter(routes)方法，同时我们可以将转后的路由信息保存于vuex，这样我们可以在我们的SideBar组件中获取我们的全部路由信息，并且渲染我们的左侧菜单栏，让动态路由实现。

## 具体实现

### 一、公共路由定义

首先在本地配置好固定不变的路由地址，例如登录，404这些页面,home页等。需要导出
```

export const constantRouterMap = [
  {
    path: '/login',
    name: 'login',
    meta: {
      title: 'Login - 登录',
      hideInMenu: true,
      iframeShow: 0
    },
    component: () => import('@/view/login/login.vue')
  },
  {
    path: '/',
    name: '_home',
    redirect: '/home',
    component: Main,
    meta: {
      hideInMenu: true,
      notCache: true,
      keepAlive: true
    },
    children: [
      {
        path: '/home',
        name: 'home',
        meta: {
          hideInMenu: true,
          title: '首页',
          notCache: true,
          icon: 'md-home',
          keepAlive: true,
          iframeShow: 0
        },
        component: () => import('@/view/single-page/home')
      }
    ]
  },
  {
    path: '/500',
    name: 'error_500',
    meta: {
      hideInMenu: true
    },
    component: () => import('@/view/error-page/500.vue')
  },
  {
    path: '*',
    name: 'error_404',
    meta: {
      hideInMenu: true
    },
    component: () => import('@/view/error-page/404.vue')
  }
  export default constantRouterMap
```

注意在router配置文件index.js中引入  

```
import Vue from 'vue'
import Router from 'vue-router'
import routes from './routers'
import store from '@/store'
import iView from 'iview'
import { setTitle, formatRouter } from '@/libs/util'

Vue.use(Router)
const router = new Router({
  routes,
  mode: 'hash'
})

```

### 二.获取菜单数据信息

这里我直接上代码并在代码中说明每一步的作用和实施

```

首先要对beforeEach钩子函数有一定的了解，这个就不多说了。
router.beforeEach((to, from, next) => {
  // 这句是iview的一个加载条效果可以忽略
  iView.LoadingBar.start() 
  // 我这个项目没有固定字段判断是否登录，而是用一下调用菜单接口来判断，进入除login都会调用一次菜单接口，没有成功说明没有登录
  if (to.name === LOGIN_PAGE_NAME) {
    //判断是调到登录页可以跳转
    next()
  } else {
    // !store.getters.getHasGetRouter这个参数是存放在vuex中用户为调用菜单接口，这个必须有因为刷新是会用，刷新此字段还原默认会再次调菜单接口
    if (!store.getters.getHasGetRouter) {
      store.dispatch('handleMenusReq').then(dataRows => {
        // 调用成功后将setHasGetRouter字段改掉以防止一直再调用接口
        store.commit('setHasGetRouter', true)
        if (dataRows.data.code === 1) {
          initRouter(router) // 这个是添加动态路由过程方法，方法在下面
          // 说明这个跳转必须用path，用name或next()页面刷新都会找不到页面，原因刷新是只有地址to中其他属性没有
          next({
            path: to.path
          })
        } else {
          // 没成功还在登录
          next({
            name: LOGIN_PAGE_NAME
          })
        }
      }).catch(error => {
        if (error) {
          next({
            name: LOGIN_PAGE_NAME
          })
        }
      })
    } else {
    // 已经有菜单数据正常跳转
      next()
    }
  }
})
// 添加路由方法
const initRouter = (routerData) => {
// formatRouter这个方法是将返回菜单数据转化处理为需要的路由格式，这个根据你的路由和数据自己写就可以
  const dypRouter = formatRouter(store.state.app.menusRepList)
  // 这个就是添加路由的过程，没错就这一句代码就可以是不是很简单
  routerData.addRoutes(dypRouter)
  // this.$router不是响应式的，所以手动将路由元注入路由对象
  routerData.options.routes = [...dypRouter, ...routerData.options.routes]
  // 将获取到的动态路由存到vuex中其他页面使用方便
  store.commit('setMenuRouter', dypRouter)
}

```

## 总结：

1、注意本地路由的配置，记得导出和引入要将404页面加载到下面。  

2、vuex存的store.getters.getHasGetRouter这个字段很重要一定要有，这个是解决刷新404的主要思路办法。  

3、将后台数据转化成路由格式要注意其实跟你本地配置路由格式一样只是字段不一样，这个过程多多调试注意细节。 

4、动态太路由addrouter方法调用后一定要将获取到的动态路由存到vuex中，以便使用。  

以上每个步骤都说的很清楚，如果有需要改进的，优化的欢迎讨论留言。
