---
layout:     post
title:      vue项目keep-alive实现状态保持
subtitle:   keep-alive实现组件切换状态保持
date:       2019-05-18
author:     Wsz
header-img: img/adad.jpg
catalog: true
tags:
    - vue
    - keep-alive
    - 状态保持
    - vue内置属性keep-alive
---

## 常用使用方法keep-alive

 keep-alive 是 Vue 内置的一个组件，使被包含的组件保留状态，或避免重新渲染。  
```
 //修改1.在路由表js  增加 router.meta 属性
   {
    path: '/personalInfo',
    name: 'personalInfo',
    component: Main,
    meta: {
      hideInBread: true,
      hideInMenu: true,
    },
    children: [
      {
        path: 'personalInfo_page',
        name: 'personalInfo_page',
        meta: {
          icon: 'ios-person',
          title: '个人资料',
          keepAlive: true, // 需要状态保持
        },
        component: () => import('@/view/personalInfo/personalInfo.vue')
      }
    ]
  },
  {
    path: '/editPassword',
    name: 'editPassword',
    component: Main,
    meta: {
      hideInBread: true,
      hideInMenu: true
    },
    children: [
      {
        path: 'editPassword_page',
        name: 'editPassword_page',
        meta: {
          icon: 'ios-unlock',
          title: '密码修改',
          iframeShow: 0
          keepAlive: false, // 不需要状态保持
        },
        component: () => import('@/view/editPassword/editPassword.vue')
      }
    ]
  },

  修改main.vue
  <Content class='content-wrapper'>
    <keep-alive >
        <router-view v-if='$route.meta.keepAlive' v-show='$route.meta.iframeShow === 0' />
    </keep-alive>
    <router-view v-show='$route.meta.iframeShow === 0' v-if='!$route.meta.keepAlive'/>
    <ABackTop :height='100' :bottom='80' :right='50' container='.content-wrapper'></ABackTop>
</Content>
```

这样就可以实现路由切换状态保持效果

## 高级用法

指定从什么组件进入才缓存，以及销毁缓存：先介绍我发现的网上一些博主写的有bug的方法，在介绍自己的方法。

假设这里有 3 个路由： A、B、C。要求：

　　1.   默认显示 A    

　　2.  B 跳到 A，A 不刷新 

　　3.  C 跳到 A，A 刷新
这是需求我没有实际遇到，是在别的博客看到的，如有遇到请看[小兔额乖乖](https://www.cnblogs.com/wangmaoling/p/9826063.html)博客文章，写的很详细。
