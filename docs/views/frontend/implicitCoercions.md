---
title: 基础知识
date: 2021-05-11
categories:
  - frond-end
tags :
  - base
---
### + 运算符的强制转换（左结合 left-associative）
- number + number : 数字运算
- string + string : 拼接字符串
- number + string : numbere=>string,再进行字符串拼接
```js
3 + true // 4
1 + 2 + '4' // '34'
1 +'2' + 3 // '123'
```
### * 运算符的强制转换
```js
'1' * 2 // 2
```
特殊转换
- null => 0
- undefined => NaN

关于NaN,可以通过不等于自己进行判断
```js
NaN === NaN // false
NaN !== NaN // true

function isRellayNaN(x){
  return x !== x
}
```
isNaN()
```js
isNaN(NaN) // true
isNaN("foo") // true
isNaN(undefined) // true
isNaN({}) // true
isNaN({ valueOf: "foo"}) // true
```
ES6 Number.isNaN()
```js
isNaN(NaN) // true
isNaN("foo") // false
isNaN(undefined) // false
isNaN({}) // false
isNaN({ valueOf: "foo"}) // false
```
polyfill(如core-js的)
```js
Number.isNaN = Number.isNaN || function(value){
  return value !== value
}
```

### Boolean类型的强制转换
```js
undefined / null  => false
number +0 -0 0 NaN  => false
string "" => false
object => true
```
undefined的检测?

### typeof & instanceof
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

Prefer Primitives to Object Wrappers 基础类型优于对象包装
除了字面量方式进行声明，还可以通过调用String()、Number()、Boolean()显示的创建包装对象
（强制转换主要指使用Number、String和Boolean三个函数，手动将各种类型的值，分布转换成数字、字符串或者布尔值。）
包装对象的实例可以使用Object对象提供的原生方法，主要是valueOf方法和toString方法。
通过typeof可以清楚的指导基本数据类型和包装对象的区别，基本是基本类型，对象是对象类型。

当对number、string、boolean这三种数据类型调用方法时候，js会先将他们转换为对应的包装对象，并且这个对象是临时的，调用完包装对象的方法后，包装对象随即被丢弃。

所以请牢记：**原始数据类型是不可改变的。** 对JavaScript原始类型值设置属性是没有意义的

```js
'string'.value = 26;
'string'.value;//undefined
```

[javascirpt包装对象（wrapper object）](https://www.jianshu.com/p/7e585f06d029)

