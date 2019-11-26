---
title: ES6箭头函数
date: 2019-11-25 13:57:41
tags:

* ES6
* 箭头函数

categories:

* Coding
* javascript

---

## 箭头函数

---

## 基本用法

ES6允许使用“箭头”（ `=>` ）定义函数。

``` JS
var f = v => v;

//等同于
var f = function(v) {
    return v;
};
```

如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数的部分。

``` JS
var f = () => 5;
//等同于
var f = function() {
    return 5;
}

var sum = (num1, num2) => num1 + num2;
//等同于
var sum = function(num1, num2) {
    return num1 + num2;
}
```

如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用 `return` 语句返回。

``` JS
var sum = (num1, num2) => {
    return num1 + num2;
}
```

箭头函数可以与变量解构结合使用。

``` JS
const full = ({
    first,
    last
}) => first + ' ' + last;

// 等同于
function full(person) {
    return person.first + ' ' + person.last;
}
```

箭头函数使得表达更加简洁。

``` JS
const isEven = n => n % 2 === 0;
const square = n => n * n;
```

箭头函数的一个用处是简化回调函数。

``` JS
// 正常函数写法
[1, 2, 3].map(function(x) {
    return x * x;
});

// 箭头函数写法
[1, 2, 3].map(x => x * x);
```

另一个例子是

``` JS
// 正常函数写法
var result = values.sort(function(a, b) {
    return a - b;
});

// 箭头函数写法
var result = values.sort((a, b) => a - b);
```

下面是 rest 参数与箭头函数结合的例子。

``` JS
const numbers = (...nums) => nums;

numbers(1, 2, 3, 4, 5)
// [1,2,3,4,5]

const headAndTail = (head, ...tail) => [head, tail];

headAndTail(1, 2, 3, 4, 5)
// [1,[2,3,4,5]]
```

---

## 使用注意点

箭头函数的几个使用注意点：

* 函数体内的 `this` 对象，就是定义时所在的对象，而不是使用时所在的对象。
* 不可以当作构造函数，也就是说，不可以使用 `new` 命令，否则会抛出一个错误。
* 不可以使用 `argument` 对象，该对象在函数体内不存在。如果要用，可以用rest参数代替。
* 不可以使用 `yield` 命令，因为箭头函数不能用作 Generator 函数。

`this` 对象的指向是可变的，但是在箭头函数中，它是固定的。

``` JS
function foo() {
    setTimeout(() => {
        console.log('id', this.id);
    }, 100);
}

var id = 23;

foo.call({
    id: 42
});
```

上面代码中， `setTimeout` 的参数是一个箭头函数，这个箭头函数的定义生效是在 `foo` 函数生成时，而它的真正执行要等到100毫秒之后。如果是普通函数，执行时 `this` 应该指向全局对象 `window` ，这时应该输出 `21` 。但是，箭头函数导致 `this` 总是指向函数定义生效时所在的对象，所以输出的是 `42` 。

箭头函数可以让 `setTimeout` 里面的 `this` ，绑定定义时所在的作用域，而不是指向运行时所在的作用域。下面是另一个例子。

``` JS
function Timer() {
    this.s1 = 0;
    this.s2 = 0;
    //箭头函数
    setInterval(() => this.s1++, 1000);
    //普通函数
    setInterval(function() {
        this.s2++;
    }, 1000);
}

var timer = new Timer()
setTimeout(() => console.log('s1: ', timer.s1), 3100);
setTimeout(() => console.log('s2: ', timer.s2), 3100);
```
上面的代码中，`Timer`函数内部设置了两个定时器，分别使用了箭头函数和普通函数。  
前者的`this`绑定定义时所在的作用域（即`Timer`函数），后者的`this`指向运行时所在的作用域（即全局对象）。所以，3100毫秒之后，`timer.s1`被更新了3次，而`timer.s2`一次也没有更新。

