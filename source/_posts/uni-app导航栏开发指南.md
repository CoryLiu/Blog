---
title: uni-app导航栏开发指南
date: 2019-11-20 13:57:27
tags:
- uni-app
categories:
- Coding
- uni-app
---

>将官方社区的文章照搬过来，一方面正好在熟悉这块内容，另一方面熟练自己的md语法。  
>原文链接<https://ask.dcloud.net.cn/article/34921>

<!--more-->

uni-app 自带原生导航栏，在pages.json里配置。  
原生导航的体验更好，渲染新页面时，原生导航栏的渲染无需等待新页面dom加载，可以在新页面进入动画开始时就渲染。  

原生导航还可以避免滚动条通顶，并方便的控制原生下拉刷新。  
通过pages.json的配置，可以简单的、跨端的、高性能的开发业务。  

但原生导航栏的扩展能力有限。尤其是微信下，没有提供太多导航栏的配置。  
在App下，pages.json里每个页面的app-plus下可以设置titleNView等更多参数，可以得到比微信小程序更丰富的扩展性。  
另外，开发者也可以在必要时取消原生导航栏，使用view自行绘制导航栏。

## **原生导航栏的通用配置**

原生导航栏的配置，均在pages.json里，每个page下面的style配置中的navigationBar各个参数配置，即为通用配置，小程序、app、h5均生效。参考<https://uniapp.dcloud.io/collocation/pages?id=style>

## **全局取消原生导航栏**

在pages.json的globalStyle里，有个navigationStyle设置，默认是default，即带有原生导航栏。也可以设置为custom。  
在设为custom后，所有页面都没有原生导航。  
但在微信小程序里，右上角始终都有一个胶囊按钮。  
很多微信小游戏界面也没原生导航栏，但有胶囊按钮。  
一般App里不会使用这个参数配置。建议个别页面单独设置不使用原生导航，具体如下。

## **单独去除原生导航栏**

自微信客户端7.0.0起，App端HBuilderX2.0.3起，支持通过如下方法取消单独一个页面的原生导航。但小程序右上角胶囊按钮仍然去不掉。页面配置 navigationStyle 为 custom:   

```
{
    "path" : "pages/log.log",
    "style" : {
        "navigationStyle":"custom"
    }
}
```

