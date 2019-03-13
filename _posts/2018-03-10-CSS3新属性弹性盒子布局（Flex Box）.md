---
layout:     post
title:      CSS3弹性盒子布局详细介绍
subtitle:   CSS3弹性盒更加适合移动端项目使用
date:       2018-03-10
author:     Wsz
header-img: img/koaloImg/koalaBanner.png
catalog: true
tags:
    - CSS3
    - 弹性盒子
---

## 前言

性盒子是CSS3的一种新布局模式。
CSS3 弹性盒（ Flexible Box 或 flexbox），是一种当页面需要适应不同的屏幕大小以及设备类型时确保元素拥有恰当的行为的布局方式。
引入弹性盒布局模型的目的是提供一种更加有效的方式来对一个容器中的子元素进行排列、对齐和分配空白空间。

### koala使用

 简单介绍koala的使用，具体细节的地方要你亲自尝试

 打开koala如下图：


 ![](https://raw.githubusercontent.com/wangshouzhi/wangshouzhi.github.io/master/img/koaloImg/koalaStudy.png)

 图中1加号代表添加，可以将项目添加到工具中，工具自动识别scss文件并显示

 图中2翻译成功后的.css文件放的位置，可以自行设置

 图中3是使用编译的一些特殊情况，比如有没有日志、备份、翻译成压缩版等。（有时间自己研究这里就不细说了）

 使用使项目导入，点击要编译的文件点左侧的翻译按钮即可达到翻译效果。（使用koala编译sass其实就这么简单，高效比下ruby简单方便多了）

### koala使用有几个注意事项

 1、koala使用也需要配置中文不识别配置方法：

 到安装盘D:\web前端软件\koala\rubygems\gems\sass-3.4.25\lib\sass对应安装目录下找到engine.rb文件

 添加Encoding.default_external = Encoding.find(‘utf-8’)语句

2、使用koala编译scss文件 项目地址不能带中文  否则会报以下错误

  ![](https://raw.githubusercontent.com/wangshouzhi/wangshouzhi.github.io/master/img/koaloImg/koalachane.png)

3、Scss文件的注释要用/* */这样的形式才能编译出注释，使用//注释不能编译出来。
