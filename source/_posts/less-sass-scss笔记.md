---
title: 'less,sass,scss笔记'
date: 2020-03-08 20:10:21
tags:
- css
categories:
- Coding
- 前端开发
---

## 前言（css预处理器）
好多年前，从使用bootstrap开始，就接触过less   
随着这些年前端技术的法杖，现在越来越多的 `css预处理器` 被创造出来，web开发中，美工设计将不再是从前那种切图，然后生成冗长而又难以维护的css代码了   
其实在我的理解看来，不论是less，sass，scss还是stylus，他们的诞生就是为了让css的编写更“程序化”，就像规定了一套约定和语法，然后可以通过解释与编译成css（最终生成的必定还是css，因为浏览器只认识css啊）
写下这篇笔记，梳理一下css预处理器的基础知识，方便之后自己查看

<!-- more -->

## sass
按时间顺序来说，sass应该是最早出现的css预处理器   
> 维基百科：Sass（英文全称：Syntactically Awesome Stylesheets）是一个最初由Hampton Catlin设计并由Natalie Weizenbaum开发的层叠样式表语言。在开发最初版本之后，Weizenbaum和Chris Eppstein继续通过SassScript来继续扩充Sass的功能。SassScript是一个在Sass文件中使用的小型脚本语言。

在我的理解，sass和scss其实都属于sass。最初的sass是以换行和缩进来进行书写，看起来更接近于ruby，python的感觉，文件后缀 .sass 。而后又诞生了新的语法“scss”，文件后缀 .scss，相比sass，scss的语法更接近css，因为使用花括号作为分隔。而且scss完全兼容css，这就意味着，你可以直接复制从前的css到新的.scss文件中。

## less
2009年出现，受SASS的影响较大，但又使用CSS的语法，让大部分开发者和设计师更容易上手，在ruby社区之外支持者远超过SASS，其缺点是比起SASS来，可编程功能不够，不过优点是简单和兼容CSS，反过来也影响了SASS演变到了SCSS的时代，著名的Twitter Bootstrap就是采用LESS做底层语言的。

## 相关链接
> [less官网](!http://lesscss.org/)

> [sass中文网](!https://www.sass.hk/)


