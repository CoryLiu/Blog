---
title: ES6优化vue项目代码
date: 2019-12-02 09:32:54
tags:

- ES6
- Vue

categories:

- Coding
- Vue.js

---

## ES6简写

``` JS
//没有简写
{
    method: {
        getValue: function() {
            //...
        },
    },
    computed: {
        halfValue: function() {
            //...
        }
    },
    created: function() {
        //...
    }
}

//使用ES6简写
{
    method: {
        getValue() {
            //...
        }
    },
    computed: {
        halfValue() {
            //...
        }
    },
    created() {
        //...
    }
}
```

## 解构

``` JS
const person = {
    name: 'Jake',
    email: 'jake@example.com',
    phone: '123-231-3123'
}

//没有解构
const name = person.name
const email = person.email
const phone = person.phone

//使用解构
const {
    name,
    email,
    phone
} = person
```

### Vue中主要两个领域适合做解构： 从 this 中解构属性；从作用域插槽接收 prop。

### 从this解构

``` JS
data() {
        return {
            endpoint: 'example.com/api',
        }
    },
    methods: {
        postForm() {
            submitForm() {
                //没有解构
                const endpoint = this.endpoint
                const postForm = this.postForm

                //使用解构
                const {
                    endpoint,
                    postForm
                } = this
            }
        }
    }
```

解构方式可以不用this前缀就使用这些变量，也可以清楚知道函数依赖于哪些数据和方法。

### 作用域插槽

``` HTML
<!-- 不使用解构 -->
<User v-slot="slotProps">
    <div>Name: {{ slotProps.name }}</div>
    <div>Email: {{ slotProps.email }}</div>
</User>

<!-- 使用解构 -->
<User v-slot="{name,email}">
    <div>Name: {{name}}</div>
    <div>Email: {{email}}</div>
</User>
```

相比“从 this 解构”的模式，解构插槽 prop 不仅让我们可以不用 slotProps 前缀就访问变量，还向我们展示了通过插槽接收了哪些属性。

## 新数组方法

map、排序数组、过滤数组   
在Vue中常用的数组方法 filter、map、forEach、includes  

``` JS
//filter示例
computed: {
    //不使用数组方法
    oldFilteredItems() {
        const filtered = []
        for (const item in this.items) {
            if (item.value > 10) {
                filtered.push(item)
            }
        }
        return filtered
    },
    //使用数组方法
    filteredItems() {
        return this.items.filter((item) => item.value > 10)
    }
}
```

## 箭头函数
``` JS
data() {
	return {
		scrolled: false
	}
},
mounted() {
	window.addEventListener('scroll', function() {
		this.scrolled = true
	})
}
```
以上代码并不工作。创建新函数的时候，this 的值会重新绑定为等于函数实例，而不是Vue实例。   
以下是解决方案：
``` JS
mounted() {
	var self = this
	window.addEventListener('scroll', function() {
		self.scrolled = true
	})
}
```
虽然解决了问题，但是代码里`var self = this` 绝对难看，所以使用箭头函数解决此类问题   
``` JS
mounted() {
	window.addEventListener('scroll', () => {
		this.scrolled = true
	})
}
```
>箭头函数和this的关系，参照另一篇文章[ES6箭头函数](/2019/11/25/ES6箭头函数/)，使用箭头函数替换标准函数，不会重新绑定this。

编写Vue应用的时候，注意此规则：`在Vue组件中this应始终引用Vue实例。用好箭头函数的话这个目标很容易实现，这样你的代码就更容易理解了。`




>[中文原文链接](https://www.infoq.cn/article/mf_x3oizVrSruwpTevH5)  
[英文原文链接](https://blog.logrocket.com/cleaning-up-your-vue-js-code-with-es6/)