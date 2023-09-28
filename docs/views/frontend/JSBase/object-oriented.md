In other object-oriented programming languages, the this keyword always refers to the current instance of the class. Whereas in JavaScript, the value of this depends on how a function is called.
---
title: 面向对象——原型、this、class
date: 2023-9-26
categories:
  - frond-end
tags :
  - this
  - prototype
  - class
  - base
---
## 面向对象
### 构造函数
js中没有类，类对应的是构造函数。
**构造函数**：如果一个函数返回的是对象，就是构造函数

### 封装、继承、多态
封装、继承和多态并不是面向对象独有的，面向对象是给了一个写代码的思路。
1. 封装：隐藏细节。
  - A对A隐藏细节，减轻思维负担，便于写成更有条理的代码：如将一段同一作用的代码，封装成一个函数或者对象。
  - A对B隐藏细节，有利于合作：A提供接口，B传入url $.get(url).then()
2. 继承：复用代码
  - 比如有基础窗口，弹窗可以继承自基础窗口，然后添加自己独有的方法。
3. 多态：使其更加灵活（比如其他语言，如果没有多态，可能就需要经常进行类型转换）
  - A拥有多种属性，比如一个<div>，既可以是节点，也可以是元素，可以使用它们对应的API。

## 原型
### 数据类型
基本类型：number/string/bool/undefined/null/symbol 存储的是数值
引用类型：object 存储的是地址（栈stack）

### 原型链
原型 __proto__ ：共有属性
`注意：`不要直接访问__proto__属性，会造成性能降低。

window.Object.prototype / Objectgit.prototype

可以认为是实例化，也可以认为是继承

### 对象
1. 普通对象
2. 数组：下标是特定的
3. 函数

## this
In other object-oriented programming languages, the this keyword always refers to the current instance of the class. Whereas in JavaScript, the value of this depends on how a function is called.

We know that functions are a special kind of objects in JavaScript. So they have access to some methods and properties. JavaScript also provides some special methods and properties to every function object. So every function in JavaScript inherits those methods. Call, bind, and apply are some of the methods that every function inherits.
### this
在函数执行时，this 总是指向调用该函数的对象。要判断 this 的指向，其实就是判断 this 所在的函数属于谁。

# 全局上下文
非严格模式和严格模式中this都是指向顶层对象（浏览器中是window）。
# 函数上下文
## 普通函数调用模式
```js
// 非严格模式
var name = 'window'
var doSth = function() {
  console.log(this.name)
}
doStn() // 'window'

let name2 = 'window2'
let doSth2 = function() {
  console.log(this)
  console.log(this.name2)
}
doStn2() // true undefined

// 严格模式
// 普通函数中的this则表现不同，表现为undefined。
'use strict'
var name3 = 'window3'
var doSth3 = function() {
  console.log(typeof this === 'undefined')
  console.log(this.name3)
}
doStn3() // true  报错,因为this为undefined
```
## 对象中的函数调用模式
```js
var name = 'window'
var doSth = function() {
  console.log(this.name)
}
var student = {
  name: 'Jack',
  doSth: doSth,
  otherStudent: {
    name: 'Bob',
    doSth: doSth
  }
}

student.doSth() // 'Jack' student.doSth.call(student)
student.otherStudent.doSth() // 'Bob' student.otherStudent.doSth.call(student.otherStudent)
```