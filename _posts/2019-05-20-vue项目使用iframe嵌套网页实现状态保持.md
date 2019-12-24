---
layout:     post
title:      vue项目使用iframe嵌套页面实现状态保持
subtitle:   iframe嵌套第三方页面实现状态保持
date:       2019-05-20
author:     Wsz
header-img: img/adad.jpg
catalog: true
tags:
    - vue
    - iframe嵌套
    - 状态保持
---

## 问题描述

  vue项目当使用路由跳转页面，页面是使用iframe标签引入第三方网页，切换路由再切换回来时网页会重新加载，不会保持切换走时状态，问题分析是vue-router切换过程页面重新加载，或者说iframe引入页面的过程重新进行，导致不能保持状态。
  
## 思路

解决思路配置路由当跳转的是iframe页面路由隐藏，直接打开显示的是一个组件，组件内容是tabs选项卡页面，利用选项卡控制打开的页面是什么？同理如果不是iframe页面直接显示路由。

## 具体实现

### 一、路由字段添加

首先在本地配置好固定字段，我这里是在meta中添加iframeShow,0代表走路由，1代表走iframe，这块可以按照自己喜欢来配置或需求来配置，注意：动态路由也需要加，别忘了。

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
  
```

### 二.路由配置组件配置（main.vue）

我的项目是配置的是main.vue是一个主组件包括导航、左侧菜单、头部等。

```
<Content class='content-wrapper'>
    <keep-alive >
        <router-view v-if='$route.meta.keepAlive' v-show='$route.meta.iframeShow === 0' />
    </keep-alive>
    <router-view v-show='$route.meta.iframeShow === 0' v-if='!$route.meta.keepAlive'/>
    这个iframe-page是单独写的iframe引入组件，下面会细说这个组件，这里主要注意v-show的使用，一定要是show不能用if，使用if达不到效果，至于为什么看一下v-show和v-if特性就知道了。
    <iframe-page v-show='$route.meta.iframeShow === 1' ></iframe-page>
    <open-out v-if='$route.meta.iframeShow === 2'></open-out>
    <ABackTop :height='100' :bottom='80' :right='50' container='.content-wrapper'></ABackTop>
</Content>
```
### 三.iframe-page组件开发
iframe-page组件开发主要有两点。  

第一个是怎样实现tabs选项卡效果，这个iview有tabs直接可以用，这里切换标识要与router切换标识一样，该项目用的是name字段。

```

这是tabs代码使用iview组件库中的tabs，一看就会明白
<template>
    <Tabs type="card" :value="tabValue" class="iframe_page" :animated="animated">
        <TabPane v-for="(tab) in iframeTabs" :key="tab.menuName" :label="'标签'+tab.menuName" :name="tab.menuName">
            <iframe class="iframe_page_src"  frameborder="0" :src="tab.resource" sandbox="allow-forms allow-scripts allow-same-origin allow-popups">  
            </iframe>
        </TabPane>
    </Tabs>
</template>
<script>
import store from '@/store'
export default {
    name: "IframePage",
    data() {
        return{
        }
    },
    computed: {
        iframeTabs() {
            return this.$store.getters.getIframeTabs
        },
        tabValue() {
            return this.$store.state.app.tabValue
        }
    }
}
</script>
```

第二点是tabs选项卡中iframeTabsPage数据怎么来，每页iframe地址数据。  

这里有两个思路：

1.通过登录后初始化菜单数据时将项目中全部第三方地址数据iframeTabsPage存到本地和vuex中并直接渲染iframe-page组件（但是尝试这种方法如果嵌套的网页太多会导致浏览器崩溃，所以不建议使用，如果嵌套页面少可以考虑还是很方便）。  

2.写一个方法在项目点击菜单路由跳转的同时从菜单接口数据中获取出对应地址等数据，如果不是就不用获取，获取的数据要存到vuex和本地，方便使用，这样只有访问页面时iframeTabsPage才会对应数据，这就不会使浏览器崩溃，这里还要注意存储数据要跟tag面包屑数据要对应，同时关闭标签某个页面对应数据也要删掉的。这里只是简单展示获取数据的地方，获取数据的具体方法就不展示了，根据自己项目、数据、需求写一个就可以。

```

这个是点击左侧菜单后的点击事件
turnToPage(route) {
      this.collapsed = true
      let { name, params, query } = {};
      let routerData = this.$router.options.routes
      if (typeof route === 'string') {
        name = route;
        // 这个是获取iframe中选项卡数据方法，我放放在vuex中通过name找到对应数据
        this.setIframetabs(name)
        // 这个方法是存到vuex中tabs选项卡切换到对用页面识别值name
        this.setTabValue(name)
      }else {
        name = route.name;
        params = route.params;
        query = route.query;
        this.setTabValue(route.name)
      }
      if (name.indexOf('isTurnByHref_') > -1) {
        window.open(name.split('_')[1]);
        return;
      }
      this.$router.push({
        name,
        params,
        query
      });  
    },
```

## 总结：

1.完成以上三步就可以实现vue项目iframe嵌套第三方页面状态保持效果，主思路是vue-router跳转实际显示是iframe开发组件，并且用选项卡切换。  

2.我这是写的是动态路由，所有数据是从菜单数据中获取，如果不是要从本地路由中获取，要将本地路由配置好。

