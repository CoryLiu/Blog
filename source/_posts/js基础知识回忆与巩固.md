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

# 4：前端安全问题，SQL注入，跨域安全，以及其实现原理

# 5：跨域的所有解决方案

# 6：前端项目的性能优化
