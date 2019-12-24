---
layout:     post
title:      vue项目IE9及以上兼容解决方案
subtitle:   基于iview搭建的vue项目IE兼容
date:       2019-04-06
author:     Wsz
header-img: img/adad.jpg
catalog: true
tags:
    - IE
    - iview
    - 兼容性
---

## 简介

先吐槽一下IE，IE浏览器存在就是一个Bug，所以最好不要涉足IE兼容问题中去。  
IE9及以上兼容问题主要从一下几个方面入手：ES6新语法不兼容（几乎所有的ES6语法，需要转化成ES5）、Promise（没有Promise内置对象）、兼容dataset、兼容requestAnimationFrame（ie9）、兼容classList（ie9）、flex布局不要使用、新css3样式都不要使用，乖乖的使用浮动定位就好，样式不会出错。

注意项目使用vue-cli3脚手架

## 一、安装babel-polyfill

  IE浏览器没有内置Promise对象。不仅如此，几乎所有的ES6新增的方法在IE都不能用，此时你需要babel Polyfill  

  1、命令 npm install babel-polyfill --save  

  2、修改vue.config.js
    在文件开头加上
    require('babel-polyfill')在module.exports中加一下代码  
```
    chainWebpack: config => {
    config
      .entry('polyfill')
      .add('babel-polyfill')
    config.resolve.alias
      .set('@', resolve('src')) // key,value自行定义，比如.set('@@', resolve('src/components'))
      .set('_c', resolve('src/components'))  
  },
```

## 二.Pormise的问题，原来是Axios不能直接兼容IE9

1、先去项目里下载es6-promise

npm install es6-promise

  2、然后去main.js文件中导入

import Promise from 'es6-promise';  
Promise.polyfill();

## 三.兼容dataset

 [Vue warn]: Error in directive transfer-dom inserted hook: "TypeError: 无法获取未定义或 null 引用的属性“transfer”"  

这是ie10及以下不支持dataset导致的，而iview的transfer-dom.js使用了这个属性  

解决办法：在main.js加入如下代码

``` 
if (window.HTMLElement) {  
    if (Object.getOwnPropertyNames(HTMLElement.prototype).indexOf('dataset') === -1) {
        Object.defineProperty(HTMLElement.prototype, 'dataset', {
            get: function () {
                var attributes = this.attributes; // 获取节点的所有属性
                var name = [];
                var value = []; // 定义两个数组保存属性名和属性值
                var obj = {}; // 定义一个空对象
                for (var i = 0; i < attributes.length; i++) { // 遍历节点的所有属性
                    if (attributes[i].nodeName.slice(0, 5) === 'data-') { // 如果属性名的前面5个字符符合"data-"
                        // 取出属性名的"data-"的后面的字符串放入name数组中
                        name.push(attributes[i].nodeName.slice(5));
                        // 取出对应的属性值放入value数组中
                        value.push(attributes[i].nodeValue);
                    }
                }
                for (var j = 0; j < name.length; j++) { // 遍历name和value数组
                    obj[name[j]] = value[j]; // 将属性名和属性值保存到obj中
                }
                return obj; // 返回对象
            },
        });
    }
}
```

### 四.兼容requestAnimationFrame（ie9）

ie9是不支持requestAnimationFrame的，如果你使用了出现错误，那也没关系，往下看就行了。

解决方案：添加以下代码到main.js

```

// window.requestAnimationFrame多浏览器兼容问题补丁
// http://paulirish.com/2011/requestanimationframe-for-smart-animating/
// http://my.opera.com/emoller/blog/2011/12/20/requestanimationframe-for-smart-er-animating
// requestAnimationFrame polyfill by Erik Möller. fixes from Paul Irish and Tino Zijdel
// MIT license
(function () {
    var lastTime = 0;
    var vendors = ['ms', 'moz', 'webkit', 'o'];
    for (var x = 0; x < vendors.length && !window.requestAnimationFrame; ++x) {
        window.requestAnimationFrame = window[vendors[x] + 'RequestAnimationFrame'];
        window.cancelAnimationFrame = window[vendors[x] + 'CancelAnimationFrame'] ||
            window[vendors[x] + 'CancelRequestAnimationFrame'];
    }

    if (!window.requestAnimationFrame) {
        window.requestAnimationFrame = function (callback, element) {
            var currTime = new Date().getTime();
            var timeToCall = Math.max(0, 16 - (currTime - lastTime));
            var id = window.setTimeout(function () { callback(currTime + timeToCall); },
                timeToCall);
            lastTime = currTime + timeToCall;
            return id;
        };
    }

    if (!window.cancelAnimationFrame) {
        window.cancelAnimationFrame = function (id) {
            clearTimeout(id);
        };
    }
}());

```

### 五.兼容classList（ie9）

错误信息：
无法获取未定义或 null 引用的属性“add”
无法获取未定义或 null 引用的属性“remove”  

如果你查看sourceMap发现了classList().add或classList.remove()等等，那肯定是classList的问题了。  

解决方案：添加以下代码到main.js

```

if (!('classList' in document.documentElement)) {
    Object.defineProperty(HTMLElement.prototype, 'classList', {
        get: function () {
            var self = this;
            function update(fn) {
                return function (value) {
                    var classes = self.className.split(/\s+/g);
                    var index = classes.indexOf(value);

                    fn(classes, index, value);
                    self.className = classes.join(' ');
                };
            }

            return {
                add: update(function (classes, index, value) {
                    if (!~index) classes.push(value);
                }),

                remove: update(function (classes, index) {
                    if (~index) classes.splice(index, 1);
                }),

                toggle: update(function (classes, index, value) {
                    if (~index) { classes.splice(index, 1); } else { classes.push(value); }
                }),

                contains: function (value) {
                    return !!~self.className.split(/\s+/g).indexOf(value);
                },

                item: function (i) {
                    return self.className.split(/\s+/g)[i] || null;
                },
            };
        },
    });
}
```

注意：项目兼容IE 某些css3属性不要使用，现已知道flex布局一定不能使用，还有axios方法封装是注意。以后有发现会再补充，如有新意见欢迎评论中提出共同探讨。
