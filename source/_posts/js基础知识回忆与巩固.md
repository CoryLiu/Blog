---
title: js基础知识回忆与巩固
date: 2020-03-12 20:59:11
tags:
- Vue
- JavaScript
categories:
- Coding
- JavaScript
---

# 1：Vue nextTick特性
> 以下内容来自 [vue2.x 官方文档](https://cn.vuejs.org/v2/api/#Vue-nextTick)
## Vue.nextTrick([callback,context])
- 参数：
  - <font color=red>{Function} [callback]</font>
  - <font color=red>{Object} [context]</font>
- 用法：
  在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更行后的DOM
  <!-- more -->
  ```js
    export default {
    data () {
      return {
        msg: 'hello'
      }
    },
    created () {
      this.initData()
    },
    methods: {
      initData () {
        console.log('initData')
        setTimeout(() => {
          this.msg = '修改msg的值'
          // DOM 还没有更新
          console.log(this) //使用箭头函数，this指向vue组件实例
          this.$nextTick(()=> {
            //DOM 更新了
            console.log(`触发了nextTrick的回调函数,msg的值已经修改,当前msg的值： ${this.msg}`)
          })
          // Promise写法
          this.$nextTick()
            .then(function(){
              //DOM更新了
            })
        }, 3000)
      }
    }
  }
  ```
  
## 需要使用nextTick的场景
当设置 vm.someData = 'new value' 时，组件不会立即重更新渲染。当你刷新队列时，组件会在下一个事件循环“tick”中更新。当需要用更新后的DOM来做点什么的时候，比如重新加载JQuery插件...   为了在数据变化之后等待 Vue 完成更新DOM，可以在数据变化之后立即使用 `Vue.nextTick(callback)` 。这样回调函数将在DOM更新完成后被调用。

  - 更多内容： [异步更新队列](https://cn.vuejs.org/v2/guide/reactivity.html#%E5%BC%82%E6%AD%A5%E6%9B%B4%E6%96%B0%E9%98%9F%E5%88%97)
## 继续了解Vue响应式原理
![](https://raw.githubusercontent.com/CoryLiu/Img-repo/master/img/data.jpg)
## 检测变化的注意事项
**Vue无法检测到对象属性的添加或删除**。因为Vue在初始化实例时对属性执行 getter/setter 转化，所以属性必须在 `data` 对象上存在才能让 Vue 将它转换为响应式。

```js
var vm = new Vue({
  data:{
    a:1
  }
})

// `vm.a` 是响应式的

vm.b = 2
// `vm.b` 是非响应式的
```

对于已经创建的实例，Vue 不允许动态添加根级别的响应式属性。
可以使用 <font color=red>Vue.set(object, propertyName, value)</font> 方法向嵌套对象添加响应式属性。

``` js
var vm = new Vue({
      el: '#app',
      data: {
        msg: 'hello',
        obj: {
          a: 1,
          b: 3
        }
      },
      beforeCreate: function () {
        console.log(this)
      },
      created: function () {
        this.a = 2
      },
      methods: {
        change() {
          this.$set(this.obj, 'c', 444) //DOM将会更新
          //this.obj.c = 444 //不会更新DOM
          this.$nextTick()
        }
      }
    })
```

# 2：前端常见的内存泄漏问题
## 三种类型的常见 JavaScript 内存泄漏
1. 意外的全局变量   
   未定义的变量会在全局对象创建一个新变量
   ``` js
   function foo(arg){
     bar = "this is a hidden global variable"
   }
   ```
   函数 foo 内部忘记使用 var ，意外创建了一个全局变量。此例泄漏了一个简单的字符串，无伤大雅，但是有更糟的情况。
   另一种意外的全局变量可能由 this 创建：
   ``` js
   function foo() {
    this.variable = "potential accidental global";
    }
    // Foo 调用自己，this 指向了全局对象（window）
    // 而不是 undefined
    foo();
   ```
   >在 JavaScript 文件头部加上 'use strict'，可以避免此类错误发生。启用严格模式解析 JavaScript ，避免意外的全局变量。   

   尽管我们讨论了一些意外的全局变量，但是仍有一些明确的全局变量产生的垃圾。它们被定义为不可回收（除非定义为空或重新分配）。尤其当全局变量用于临时存储和处理大量信息时，需要多加小心。如果必须使用全局变量存储大量数据时，确保用完以后把它设置为 null 或者重新定义。与全局变量相关的增加内存消耗的一个主因是缓存。缓存数据是为了重用，缓存必须有一个大小上限才有用。高内存消耗导致缓存突破上限，因为缓存内容无法被回收。
2. 被遗忘的计时器或回调函数
   ``` js
   var someResource = getData();
    setInterval(function() {
        var node = document.getElementById('Node');
        if(node) {
            // 处理 node 和 someResource
            node.innerHTML = JSON.stringify(someResource));
        }
    }, 1000);
   ```
3. 脱离 DOM 的引用
   有时，保存 DOM 节点内部数据结构很有用。假如你想快速更新表格的几行内容，把每一行 DOM 存成字典（JSON 键值对）或者数组很有意义。此时，同样的 DOM 元素存在两个引用：一个在 DOM 树中，另一个在字典中。将来你决定删除这些行时，需要把两个引用都清除。
   ``` js
   var elements = {
    button: document.getElementById('button'),
    image: document.getElementById('image'),
    text: document.getElementById('text')
    };
    function doStuff() {
        image.src = 'http://some.url/image';
        button.click();
        console.log(text.innerHTML);
        // 更多逻辑
    }
    function removeButton() {
        // 按钮是 body 的后代元素
        document.body.removeChild(document.getElementById('button'));
        // 此时，仍旧存在一个全局的 #button 的引用
        // elements 字典。button 元素仍旧在内存中，不能被 GC 回收。
    }
   ```
   此外还要考虑 DOM 树内部或子节点的引用问题。假如你的 JavaScript 代码中保存了表格某一个 `<td>` 的引用。将来决定删除整个表格的时候，直觉认为 GC 会回收除了已保存的 `<td>` 以外的其它节点。实际情况并非如此：此 `<td>` 是表格的子节点，子元素与父元素是引用关系。由于代码保留了 `<td>` 的引用，导致整个表格仍待在内存中。保存 DOM 元素引用的时候，要小心谨慎。
4. 闭包
   闭包是 JavaScript 开发的一个关键方面：匿名函数可以访问父级作用域的变量
   ``` js
    var theThing = null;
    var replaceThing = function () {
      var originalThing = theThing;
      var unused = function () {
        if (originalThing)
          console.log("hi");
      };
      theThing = {
        longStr: new Array(1000000).join('*'),
        someMethod: function () {
          console.log(someMessage);
        }
      };
    };
    setInterval(replaceThing, 1000);
   ```
   代码片段做了一件事情：每次调用 `replaceThing` ，`theThing` 得到一个包含一个大数组和一个新闭包（`someMethod`）的新对象。同时，变量 `unused` 是一个引用 `originalThing` 的闭包（先前的 `replaceThing` 又调用了 `theThing` ）。思绪混乱了吗？最重要的事情是，闭包的作用域一旦创建，它们有同样的父级作用域，作用域是共享的。`someMethod` 可以通过 `theThing` 使用，`someMethod` 与 `unused` 分享闭包作用域，尽管 `unused` 从未使用，它引用的 `originalThing` 迫使它保留在内存中（防止被回收）。当这段代码反复运行，就会看到内存占用不断上升，垃圾回收器（`GC`）并无法降低内存占用。本质上，闭包的链表已经创建，每一个闭包作用域携带一个指向大数组的间接的引用，造成严重的内存泄漏。
   >解决方法：在 `replaceThing` 的最后添加 `originalThing = null` 。
   

# 3：vue, angular, react框架各自的优缺点
1. React：由Fackbook创建的JavaScript UI框架。React推广了VirtualDOM并创造了新语法——JSX，JSX允许开发者在JavaScript中书写HTML。
2. Vue：Vue致力解决的问题与React一致，解决方案不同。Vue使用模板系统而不是JSX，因为模板用的是普通的HTML，所以通过Vue对现有系统的升级更加容易，不需要整体重构。
   ## 相似之处
   React与Vue存在很多相似之处，例如他们都是JavaScript的UI框架，专注于创造前端的富应用。不同于早期的JavaScript框架“功能齐全”，Reat与Vue只有框架的骨架，其他的功能如路由、状态管理等是框架分离的组件。
   ### 组件化
   React与Vue都鼓励组件化应用。这本质上说，是建议你将你的应用分拆成一个个功能明确的模块，每个模块之间可以通过合适的方式互相联系。

   ## 不同
   React和Vue在实现VirtualDOM转换为真实DOM的实现方式不同。
   Vue在渲染过程中，会跟踪每一个组件的依赖关系，不需要重新渲染整个组件树。
   React，当应用的状态被修改时，全部子组件都会重新渲染。当然，这可以通过shouldComponentUpdate这个生命周期方法来进行控制，但Vue将此视为默认的优化。

   ### 构建工具
   React可以使用Create React App (CRA)   
   Vue对应的则是vue-cli

   ### 配套框架
    Vue与React最后一个相似但略有不同之处是它们配套框架的处理方法。相同之处在于，两个框架都专注于UI层，其他的功能如路由、状态管理等都交由同伴框架进行处理。

    而不同之处是在于它们如何关联它们各自的配套框架。Vue的核心团队维护着vue-router和vuex，它们都是作为官方推荐的存在。而React的react-router和react-redux则是由社区成员维护，它们都不是官方维护的。

# 4：前端安全问题，SQL注入，跨域安全，以及其实现原理
- 攻击类型
  - XSS (Cross Site Script) 跨站脚本攻击
  - CSRF 跨站请求伪造
  - SQL注入
  - 点击劫持
  - 中间人攻击
## XSS 跨站脚本攻击
- 定义
  是指恶意攻击者利用网站没有对用户提交数据进行`转义处理`或者`过滤不足`的缺点，进而添加一些代码，嵌入到web页面中去。使得别的用户访问都会执行相应的嵌入代码。   
  从而盗取用户资料，利用用户身份进行某种动作或者对访问者进行病毒式侵害的一种攻击方式。
- 攻击类型
  - 反射型：
    - 把用户输入的数据反射给浏览器端，攻击者诱使用户点击恶意链接，或者提交一个表单或者进入一个恶意网站，注入脚本进入会被攻击者的网站，可以获取用户隐私数据（如cookie）的脚本。
  - 存储型：
    - 把用户输入的数据存储在服务器端，当浏览器请求数据时，脚本从服务器上传回并执行。这种XSS攻击具有很强的稳定性。
    - 场景：攻击者在论坛上写下一遍包含恶意JavaScript文章或评论，文章或评论发表后，所有访问该文章或评论的用户，都会在它们的浏览器中执行这段恶意的JavaScript代码。
  - 基于DOM：
    - 通过恶意脚本修改页面的DOM结构，是纯粹发生在客户端的攻击。
- XSS防御
  - HttpOnly防止截取cookie
  - 输入检查
    - 不要相信用户的任何输入。对用户的任何输入进行检查、过滤和转义。建立可信任的字符和HTML标签白名单，对于不在白名单之列的字符或者标签进行过滤或编码。
> 在使用vue的 v-html 的时候需要注意，比如文章内容，这些是用户填写的，不可以直接渲染显示(用户可能会使用`<script src>`导致看这个文章的人会加载外站js从而做些不可控的事情)。

可以使用 `xss` 模块进行防御 `v-html=$xss(test)`
``` js
<p v-html="$xss(test)"></p>
 
import xss from 'xss'
export default {
  data () {
    return {
      test: `<a onclick='alert("xss攻击")'>链接</a>`
    }
}
 
Object.defineProperty(Vue.prototype, '$xss', {
  value: xss
})
```

## 跨站请求伪造 CSRF
- 攻击原理
  - 用户登录A网站
  - A网站确认身份，给客户端cookie
  - 用户在没有退出A网站的情况下，访问B网站
  - B网站页面向A网站发起一个请求
  - 根据B的请求，浏览器带着产生的Cookie访问A
- 防御方法
  - GET请求不对数据进行修改
  - 不让第三方网站访问到用户的cookie
    - SameSite
      - 可以对cookie设置SameSite属性。该属性设置cookie不随着跨域请求发送，该属性可以很大程度减少跨站请求伪造，但是该属性目前并不是所有浏览器都兼容。
  - 防止第三方网站请求接口
    - Refere验证
      - 通过验证Refere来判断该请求是否是第三方网站发起的，在后台接到请求的时候，可以通过请求头中的Refere请求头来判断请求来源。
      - 使用场景：不仅防范跨站请求伪造，还可以防止图片盗链
    - 请求时附带验证信息，比如验证码或者Token
      - 验证码
        - 跨站请求伪造攻击往往是在用户不知情的情况下构造了网络请求，而验证码会强制用户必须与应用进行交互，才能完成最终请求。
      - 添加Token验证
        - 服务器下发一个随机Token，每次发起请求时将Token携带上，服务器建立拦截器验证Token是否有效。
  
  **跟跨网站脚本（XSS）相比，XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。**

## SQL注入
- 定义：
  - 就是通过把SQL命令插入到WEB表单提交或输入域名或页面请求的查询字符串，后台执行SQL语句时直接把前端传入的字段拿来做SQL查询。
- 防御：
  - 不要使用动态SQL
    - 避免将用户提供的输入直接放入SQL语句中
    - 最好使用准备好的语句和参数化查询，这样更安全
  - 不要把敏感数据保留在纯文本中
    - 加密存储在数据库中的私有/机密数据
    - 这样可以提供了另一级保护，以防攻击者成功地排出敏感数据。
  - 限制数据库权限和特权
    - 将数据库用户的功能设置为最低要求
    - 这将限制攻击者在设法获取权限时可以执行的操作
  - 避免直接向用户显示数据库错误
    - 攻击者可以使用这些错误消息来获取有关数据库的信息
  - 对访问数据库的WEB应用程序使用WEB应用程序防火墙（WAF）
    - 这为面向Web的应用程序提供了保护，它可以帮助识别SQL注入尝试
    - 根据设置，它还可以帮助防止SQL注入尝试到应用程序（以及数据库）
  - 定期测试与数据交互的Web应用程序
    - 这样做可以帮助捕获可能允许的SQL注入的新错误或回归
  - 将数据库更新为最新的的可用修补程序
    - 这可以防止攻击者利用旧版本存在的已知弱点/错误
  
## 点击劫持
- 原理：点击劫持是一种视觉欺骗的攻击手段，攻击者将需要攻击的网站通过iframe嵌套的方式嵌入到自己的网页中，并将iframe设置为透明，在页面中透出一个按钮诱导用户点击。
- 防御方法：
  - X-FRAME-OPTIONS
  - Js防御
     ``` js 
      // css中设置 body{display:none !important;}
       if(self == top) { // window.top是读取窗口的引用    window.top是顶层窗口
              document.getElementsByTagName('body')[0].style.display = 'block';
          }else {
              top.location = self.location; //;localtion：该对象包含当前URL信息 拥有多个属性
          }
      ```
## 中间人攻击
- 原理：
  - 中间人攻击是攻击方同时与服务端和客户端建立起了连接，并让双方认为连接是安全的，但是实际上整个通信都被攻击者控制了。
  - 攻击者不仅能获得双方的通信信息，还能修改通信信息。

# 5：跨域的所有解决方案
> [详细引用文章](https://segmentfault.com/a/1190000011145364)
## 需要跨域的原因
浏览器的 `同源策略` SOP (same orgin policy) ，是浏览器核心最基本的安全功能。   
所谓同源是指 `"协议+域名+端口"` 三者相同，即便两个不同的域名指向同一个ip地址，也非同源。
## 跨域解决方案
1. jsonp
2. document.domain + iframe跨域
3. location.hash + iframe跨域
4. window.name + iframe跨域
5. postMessage跨域
6. 跨域资源共享（CORS）
7. nginx代理跨域
8. nodejs 中间件代理跨域
9. WebSocket协议跨域
## jsonp跨域
缺点：只能实现get请求跨域
## iframe的三种跨域
## postMessage跨域
## 跨域资源共享（CORS）
普通跨域请求：服务端设置Access-Control-Allow-Origin即可，前端无须设置，若要带cookie请求：前后端都需要设置。
``` js
// 前端设置是否带cookie
withCredentials = true;
```
## nginx代理跨域
nginx配置
``` nginx
server {
    listen       81;
    server_name  www.domain1.com;

    location / {
        proxy_pass   http://www.domain2.com:8080;  #反向代理
        proxy_cookie_domain www.domain2.com www.domain1.com; #修改cookie里域名
        index  index.html index.htm;

        # 当用webpack-dev-server等中间件代理接口访问nignx时，此时无浏览器参与，故没有同源限制，下面的跨域配置可不启用
        add_header Access-Control-Allow-Origin http://www.domain1.com;  #当前端只跨域不带cookie时，可为*
        add_header Access-Control-Allow-Credentials true;
    }
}
```
## WebSocket协议跨域
关键词：Scoket.io模块

# 6：前端项目的性能优化
前端项目的性能优化手段，如果都罗列出来，可以写一本书了。。。   
暂时记录几个原则，日后在实际项目中遇到对应的情况再详细补充笔记   
- 请求数量——合并脚本和样式表，CSS Sprites，拆分初始化负载，划分主域
- 请求带宽——开启Gzip，精简JavaScript，移除重复脚本，图像优化
- 缓存利用——使用CDN，使用外部JavaScript和CSS，添加Expires头，减少DNS查找，配置ETag，使Ajax可缓存
- 页面结构——将样式表放在顶部，将脚本放在底部，尽早刷新文档的输出
- 代码校验——避免CSS表达式，避免重定向

## WebP图片格式
## vue项目中，将小图片直接转化base64，减少请求
## 优化项目的切入点
- 减少客户端请求
- 减少服务器端响应的体积（如gzip压缩）
- 客户端优化 dom，css，js的代码加载顺序；或使用服务器端渲染，减少客户端渲染压力（vue中的 `ssr` 技术。[相关链接](https://cn.vuejs.org/v2/guide/ssr.html)）
- 网络优化，比如增加 CDN 缓存；或增加并发处理能力，比如服务端设置多个域名，客户端使用多个域名同时请求资源，增加并发量。