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

# 3：vue, angular, react框架各自的优缺点

# 4：前端安全问题，SQL注入，跨域安全，以及其实现原理

# 5：跨域的所有解决方案

# 6：前端项目的性能优化
