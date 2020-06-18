---
title: H5唤起APP的方式
date: 2020-06-18 09:15:06
tags:
- Html5
- Scheme
categories:
- Codding
- 前端开发
---

# 目的
H5首页作为落地页，落地页承担的职责就是引流。引流有两种形式，同时也是我们对唤端的定义：引导已下载用户打开APP，引导未下载用户下载APP。

<!-- more -->

# 唤端媒介

## URL Scheme
### 来源
URL Scheme
```
[schema:][//authority][path][?query][#fragment]
```
### 常用APP的 URL Scheme
| APP        | 微信      | 支付宝    | 淘宝      | 微博         | QQ     | 知乎     | 短信   |
| ---------- | --------- | --------- | --------- | ------------ | ------ | -------- | ------ |
| URL Schema | weixin:// | alipay:// | taobao:// | sinaweibo:// | mqq:// | zhihu:// | sms:// |

### URL Scheme 语法
```
     行为(应用的某个功能)    
            |
scheme://[path][?query]
   |               |
应用标识       功能需要的参数

```
## 判断唤端是否成功
如果唤端失败（APP 未安装），我们总是要做一些处理的，可以是跳转下载页，可以是 ios 下跳转 App Store... 但是Js 并不能提供给我们获取 APP 唤起状态的能力，Android Intent 以及 Universal Link 倒是不用担心，它们俩的自身机制允许它们唤端失败后直接导航至相应的页面，但是 URL Scheme 并不具备这样的能力，所以我们只能通过一些很 hack 的方式来实现 APP 唤起检测功能。

``` js
// 一般情况下是 visibilitychange 
const visibilityChangeProperty = getVisibilityChangeProperty();
const timer = setTimeout(() => {
  const hidden = isPageHidden();
  if (!hidden) {
    cb();
  }
}, timeout);

if (visibilityChangeProperty) {
  document.addEventListener(visibilityChangeProperty, () => {
    clearTimeout(timer);
  });

  return;
}

window.addEventListener('pagehide', () => {
  clearTimeout(timer);
});

```

APP 如果被唤起的话，页面就会进入后台运行，会触发页面的 visibilitychange 事件。如果触发了，则表明页面被成功唤起，及时调用 clearTimeout ，清除页面未隐藏时的失败函数（callback）回调。
当然这个事件是有兼容性的，具体的代码实现时做了事件是否需要添加前缀（比如 -webkit- ）的校验。如果都不兼容，我们将使用 pagehide 事件来做兜底处理。

## 开箱即用的callapp-lib
 npm 包 [callapp-lib](https://www.npmjs.com/package/callapp-lib)

 calapp-lib [文档](https://github.com/suanmei/callapp-lib)

 

> 作者：_拾邑   
链接：https://juejin.im/post/5b7efb2ee51d45388b6af96c   
来源：掘金   
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
