---
layout:     post
title:      vue-grid-layout与Echart项目中使用问题
subtitle:   vue-grid-layout网格中使用Echart初始表格不能显示，刷新不显示，大小自适应等问题
date:       2018-08-10
author:     Wsz
header-img: img/adad.jpg
catalog: true
tags:
    - vue-grid-layout
    - Echart
    - 初始化显示问题
    - 创建显示问题
    - 放大缩小自适应问题
---

## 项目介绍

   使用vue创建的项目，要求首页可以拖拽缩放更能，为实现业务需求在网上找到vue-grid-layout插件 [vue-grid-layout插件gitHub地址](https://github.com/jbaysolutions/vue-grid-layout)，该插件总体使用是不错的，如有需求可以入手使用，本篇文章主要介绍使用vue-grid-layout网格布局时网格内容Echart的使用，出现的问题初始化图标不显示、图标自适应显示问题。

### vue-grid-layout安装使用

   vue-grid-layout安装及使用请参考[vue-grid-layout插件gitHub地址](https://github.com/jbaysolutions/vue-grid-layout)gitHub上下载安装使用，对应字段代表的意思，作用类型写的很全，一个就懂了，这里就不多说了。

### 问题说明展示

  1、首先是首次进到页面和页面刷新Echart图表不显示，查找原因Echart图标有加载，只是显示容器没有宽高所以看不到，但是查看

  网格布局宽高是正常的，说明问题出在vue-grid-layout加载宽高没有完成Echart图表创建加载已经完成导致Echart图表盒子加载

  宽高为0，解决办法给Echart加载加上异步效果，通过异步最好加载就可以解决了。

  ![问题效果图](http://wangshouzhi/wangshouzhi.github.io/master/img/vue-grid-layout/vu.png).

  2、网格布局拖拽放大缩小图表不能自适应，Echart只是固定宽高。

### 问题解决办法

  1、解决办法其实不难，只是当时不知问题所在，所以不知道如何解决，给Echart 图表创建加载过程加一个延时setTimeout，使加载Echart加载过程为异步就可以了，直接展示一下代码

  (```)

      methods: {
         echartDom () {
            let that =this
            //使用setTimeout异步特性使图标正常加载
            setTimeout (()=>{
               this.$nextTick(() => {
                  this.mychart = echarts.init(document.getElementById(this.echartID))
                  this.resizeDiv = document.getElementById(this.echartID)
                  this.mychart.setOption(this.echartHome)
                  let listener =function () {
                     that.mychart.resize()
                  }
                  EleResize.on(this.resizeDiv, listener)
               })
            },0)
         },
      },

  (```)

  问题解决了，轻松又愉快，当然可能还有更加方便合适的

  2.拖拽自适应问题

   解决方法利用div使用onresize方法，这样既可达到自适应效果，具体方法步骤请参考另一篇博客[Echart自适应解决办法](http://wangshouzhi.top/2018/08/06/Echart%E8%87%AA%E9%80%82%E5%BA%94%E8%A7%A3%E5%86%B3%E5%8A%9E%E6%B3%95/)
