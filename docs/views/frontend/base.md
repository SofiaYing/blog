---
title: 基础知识
date: 2020-12-23
categories:
  - frond-end
tags :
  - base
---
## typeof & instanceof
typeof 返回一个值的数据类型
```js
typeof 123 // "number"
typeof '123' // "string"
typeof false // "boolean"
typeof null // "object"  历史遗留问题
typeof undefined // "undefined" 
if (typeof v === "undefined") {} //typeof可以用来检查一个没有声明的变量，返回“undefined”而不报错
 
function f() {}
typeof f // "function"

typeof window // "object"
typeof {} // "object"
typeof [] // "object"
```
instanceof 返回一个布尔值，表示对象是否为某个构造函数的实例
- instanceof检查整个原型链
- instanceof运算符只能用于对象，不适用原始类型的值
- undefined和null，instanceOf运算符总是返回false
```js
var d = new Date();
d instanceof Date // true
d instanceof Object // true
```