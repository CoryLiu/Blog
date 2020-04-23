---
title: Promise应用场景
date: 2020-04-21 16:56:35
tags:
- JavaScript
- ES6
- Promise
categories:
- Coding
- JavaScript
---

# Promise.all
**语法：Promise.all(iterable)**   
**参数：一个可迭代对象，如Array**   
**返回值**   
  
- 如果传递的iterable为空，则是已解决的Promise。
  ``` js
  Promise.all([]).then(res=>{
    console.log(res)
  })
  ```
- 异步解析的Promise（如果传递的Iterable不包含Promise）。 请注意，在这种情况下，Google Chrome 58返回已解决的承诺。
  ``` js
  Promise.all([1,2,3]).then(res=>{
    console.log(res)//[1,2,3]
  })
  ```
  <!-- more -->
- 当给定可迭代对象中的所有promise已解决，或者任何promise均被拒绝时，此返回的promise将被异步解析/拒绝（堆栈为空时）
  - 当给定可迭代对象中的所有promise 已解决
    ``` js
    let promise1 = new Promise((resolve,reject)=>{
        resolve(1)
    })
    let promise2 = new Promise((resolve,reject)=>{
        resolve(2)
    })

    Promise.all([promise1,promise2,3]).then(res=>{
        console.log(res)//[1,2,3]
    })
    ```
  - 

