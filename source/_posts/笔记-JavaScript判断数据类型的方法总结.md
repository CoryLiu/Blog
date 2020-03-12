---
title: 笔记-JavaScript判断数据类型的方法总结
date: 2020-03-12 12:22:02
tags:
- JavaScript
categories:
- Coding
- JavaScript
---

# 1: typeof
`typeof` 操作符返回一个`字符串`，表示未经计算的操作数的类型。

## 语法

>typeof operand   
>typeof(operand)

 *注: operand [ˈɑːpərænd] n. [计] 操作数*

 <!-- more -->

### 参数
**operand**

一个表示对象或原始值的表达式，其类型将被返回

## 描述

| 类型                                                 | 结果        |
| ---------------------------------------------------- | ----------- |
| Undefined                                            | "undefined" |
| Null                                                 | "object"    |
| Boolean                                              | "boolean"   |
| Number                                               | "number"    |
| BigInt [详细](https://zhuanlan.zhihu.com/p/36330307) | "bigint"    |
| String                                               | "string"    |
| Symbol(ES2015新增的数据类型)                         | "symbol"    |
| Function对象                                         | "function"  |
| 其他任何对象                                         | "object"    |
___
## 注意！！！ null
> typeof null === 'object';

在 JavaScript 最初的实现中，JavaScript 中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是 0。由于 null 代表的是空指针（大多数平台下值为 0x00），因此，null 的类型标签是 0，typeof null 也因此返回 "object"。

曾有一个 ECMAScript 的修复提案（通过选择性加入的方式），但被拒绝了。该提案会导致 typeof null === 'null'。

## new
``` js
// 除 Function 外的所有构造函数的类型都是 'object'
var str = new String('String');
var num = new Number(100);

typeof str; // 返回 'object'
typeof num; // 返回 'object'

var func = new Function();

typeof func; // 返回 'function'
```

# 2: toString
toString 是 object 的原型方法，调用该方法，默认返回当前对象的 Class 。   
对于 Object 对象，直接调用 toString()  就能返回 [object Object] 。而对于其他对象，则需要通过 call / apply 来调用才能返回正确的类型信息。
``` js
Object.prototype.toString.call('') ;   // [object String]
Object.prototype.toString.call(1) ;    // [object Number]
Object.prototype.toString.call(true) ; // [object Boolean]
Object.prototype.toString.call(Symbol()); //[object Symbol]
Object.prototype.toString.call(undefined) ; // [object Undefined]
Object.prototype.toString.call(null) ; // [object Null]
Object.prototype.toString.call(new Function()) ; // [object Function]
Object.prototype.toString.call(new Date()) ; // [object Date]
Object.prototype.toString.call([]) ; // [object Array]
Object.prototype.toString.call(new RegExp()) ; // [object RegExp]
Object.prototype.toString.call(new Error()) ; // [object Error]
Object.prototype.toString.call(document) ; // [object HTMLDocument]
Object.prototype.toString.call(window) ; //[object global] window 是全局对象 global 的引用
```

# 3: constructor

constructor 是原型 prototype 的一个属性，当函数被定义时候，js 引擎会为函数添加原型prototype，并且这个 prototype 中 constructor 属性指向函数引用， 因此重写 prototype 会丢失原来的constructor。

<font color=red>**不过这种方法有问题：**</font>
1. null 和 undefined 无constructor，这种方法判断不了。
2. 如果自定义对象，开发者重写prototype之后，原有的constructor会丢失，因此，为了规范开发，在重写对象原型时一般都需要重新给 constructor 赋值，以保证对象实例的类型不被篡改。

``` js
''.constructor === String //true
new Number(1).constructor === Number //true
true.constructor === Boolean //true
new Function().constructor === Function //true
new Date().constructor === Date //true
new Error().constructor === Error //true
[].constructor === Array //true
document.constructor === HTMLDocument //true
window.constructor === Window //true
```

# 4:instanceof
字面翻译理解，`instance` 实例，即
instanceof 是用来判断 A 是否为 B 的实例，表达式为：A instanceof B，如果 A 是 B 的实例，则返回 true,否则返回 false。 在这里需要特别注意的是：instanceof 检测的是原型

![](https://raw.githubusercontent.com/CoryLiu/Img-repo/master/img/%E6%8D%95%E8%8E%B7.JPG)


[]的原型指向 Array.prototype, 间接指向 Object.prototype
```js
 [] instanceof Array === true //true
 [] instanceof Object === true //也是true
```
所以 instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。（JavaScript特殊的原型链机制）