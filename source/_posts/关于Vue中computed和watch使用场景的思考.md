---
title: 关于Vue中computed和watch使用场景的思考
date: 2019-10-16 14:09:45
tags:
- Vue.js
categories:
- Coding
- Vue.js
---
# 为何需要计算属性（computed）
``` html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```
需要对data进行一些简单的计算与操作的时候，如上代码，直接在模板中放入逻辑。假如这个操作比较复杂，则对于页面后续的维护不易。假如页面中多个地方都需要进行相同的计算，那岂不是都需要复制一遍，当计算规则改变时，岂不是每个地方都需要进行修改。那么这种方式明显是不合适的。   
所以vue提供了`计算属性`这一特性：
## 基础例子
``` html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```
``` js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```
vm.reversedMessage依赖于vm.message，所以当vm.message发生改变的时候，所有依赖vm.reverseMessage的绑定也会更新。
## 注意：
与直接在模板中使用method的方式比较，使用computed只有在依赖的数据发生改变时才会进行计算，也就是它是有缓存的，使用method，则每次访问reverseMessage都将进行一次新的计算。这无疑对性能是不好的。

# 侦听属性（watch）
vue官方的说法是，在需要数据变化的时候执行异步或开销较大的操作时(在computed中无法做到异步操作，异步函数执行之后，回调函数中返回值并不会触发dom的重新渲染)，使用watch

异步ajax请求的发起，可以使用 [Lodash](https://lodash.com/) 模块的 [_.debounce](https://lodash.com/docs#debounce) 进行请求发起的间隔，防止修改数据过于频繁，导致ajax请求无限发送，影响性能。
