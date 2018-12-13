---
layout: post
title: 学习sass编译工作
subtitle： ruby安装使用，koala安装使用
date: 2018-06-06
autor:Wsz
header-img:
catalog:true
tags: sass必备
---
一、SASS的安装
  SASS是ruby语言写的 因此先安装Ruby
  ruby安装包网址可以从http://rubyinstaller.org/
 注：下载后按步骤安装  安装路径不要变  使用默认的
  安装成功在控制台 输入ruby  打开start Command Prompt withRuby命令窗口如下图：
在命令窗口：
   输入 ruby -v 查询ruby安装的版本
   输入 gem -v 查询 rubyGem版本
   使用命令 gem install sass 安装sass
   安装完成 sass -v 查询sass版本
   查到  sass版本  sass就可以使用
   注：1、sass是日本开发  不支持中文需要到ruby安装目录下找到C:\Ruby24-x64\lib\ruby\gems\2.4.0\gems\sass-3.7.2\lib\sass目录下的engine.rb文件
  在文件下添加Encoding.default_external = Encoding.find(‘utf-8’)语句，添加位置如下图：
 2、在编写代码时在.scss文件前加 @charset "utf-8" 即可解决sass不编译带中文的文件
二、sass编译
  1、同上方法打开命令窗口
  输入d:
  输入cd “ ”将项目文件拖到引号中去，再点Enter 即可对项目中的scss文件进行编译
  编译命令   （项目中scss的文件名为t）
       命令Sass t.scss t.css -C --sourcemap=none
          -C  避免输出缓存
          --sourcemap=none  避免输出sourcemap文件




三、使用koala翻译工具
1、下载安装koala
下载地址http://koala-app.com/index-zh.html  安装使用默认地址安装
配置中文是识别：安装完成后D:\web前端软件\koala\rubygems\gems\sass-3.4.25\lib\sass对应安装目录下找到engine.rb文件 添加Encoding.default_external = Encoding.find(‘utf-8’)语句
注：使用koala编译scss文件 项目地址不能带中文  否则会报以下错误，
    Scss文件的注释要用/* */这样的形式才能编译出注释
