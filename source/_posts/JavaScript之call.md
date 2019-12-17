---
title: JavaScript之call()与apply()
date: 2019-12-09 10:40:56
tags:
- Coding
- JavaScript
- call
categories:
- Coding
- JavaScript
---

## Function.prototype.call()
`call`方法使用一个指定的`this`值和单独给出的一个或多个参数来调用一个函数。

``` JS
function Product(name, price) {
  this.name = name;
  this.price = price;
}

function Food(name, price) {
  Product.call(this, name, price);
    /* 
        错误写法： Product(name,price) 相当于 Product.call(null,name,price)，
        非严格模式下，null或者undefined会自动替换为指向全局对象，如果在浏览器下则是
        window ，所以相当于Product.call(window,name,price)
    */
  this.category = 'food';
}

console.log(new Food('cheese', 5));
// 结果: Food {name: "cheese", price: 5, category: "food"}
```

>[MDN 文档 call](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

## Function.prototype.apply()
`apply()` 方法调用一个具有给定`this`值的函数，以及作为一个数组（或类似数组对象）提供的参数。

``` JS
const numbers = [5, 6, 2, 3, 7];

const max = Math.max.apply(null, numbers);

console.log(max);
// expected output: 7

const min = Math.min.apply(null, numbers);

console.log(min);
// expected output: 2
```
### 备注：Math.max
`max` 是 `Math` 的静态方法，所以直接使用：`Math.max()`,而不是创建的`Math`实例的方法(`Math`不是构造函数，通过`typeof(Math)`可以看到，`Math`是一个`Object`,没有`prototype`)。

### 语法
> <b style="font-size:1.4em">Math.max(value1[,value2,value3,...])</b>

``` JS
Math.max(numbers)
//运行结果 NaN
```
若不使用`apply()`，运行结果为NaN，原因是相当于只提供了一个参数（numbers数组），`max`方法给定的参数无法被转换为数字，则会返回`NaN`。

>[MDN 文档 apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
