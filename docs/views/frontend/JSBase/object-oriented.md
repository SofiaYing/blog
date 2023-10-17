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



读取js代码时，第一次不执行，做词法分析，生成抽象语法树

词法作用域确定的是语义，变量关系，无法确定值。

var a = 1
function b(){
	console.log(a) //a是第一行的a
}
a=2
b()




函数是对象

sayName如何获取到name

var obj = {
	name: ‘Zhang’,
	sayName: function() { // sayName只是存了这个函数的地址。（对象在内存里是没有名字的！）
		console.log(this.name) // this连接对象和函数
	}
}

obj.sayName(obj.name) // Js的函数很纯粹，只接受参数，只输出一个返回值。是无法获取到点前面的obj的。

obj.sayName() // 隐式指定obj为this
obj.sayName.call(obj) // 显式指定obj为this，this就相当于是一个参数。




var food = {
	name: ‘fruit’,
	detail: {
		name: ‘apple’,
		sayName: function() {
			console.log(this.name)
		}
	}	
} 

food.detail.sayName() // apple
food.detail.sayName.call() / food.detail.sayName.call(undefined) // 非严格模式window, 严格模式下报错 read property ‘name’ of undefined
food.detail.sayName.call({name:’orange’}) //orange food.detail.sayName只是函数的地址，函数本身是独立于对象food存在的，不是对象的附属品（函数是一等公民），也就是说这个函数可以接受任何对象作为参数


function接受的参数this(第一个参数), arguments(其他参数) 
思考：this是参数，那么参数的值是什么时候确定的？
参数的值只有在传参的时候才确定
this是函数的第一个参数
所以this的值是在传参的时候，即函数调用的时候，才能确定。


function a() {
	console.log(this)
}
this的值是多少？ 答：不能确定
a() 
2. this的值是什么？ 答：window（浏览器中） ，global（node.js中）
function a(){
	‘use strict’
	console.log(this)
}
3. this的值是什么？ 答：undefined
var obj = {
	sayThis: a
}
obj.sayThis()
4. this的值？ 答：对象obj
obj.sayThis.call()
5. this的值？答：undefined
obj.sayThis.call(2) 
6. this的值？答：2

this的值如何确定？看文档。
button.onclick = function() {
	console.log(this)
}
this的值？答：button (文档已经规定：函数里的this是触发事件的元素)
$(‘#button).on(‘click’, function(){
	console.log(this)
})
2. this的值? 答：button (jQuery文档规定，this指向的是当前正在执行事件的元素)
$(‘#ul’).on(‘click’,’li’,function(){
	console.log(this)
})
3. this的值？答：对应的li（jQuery事件代理，this代表了与selector相匹配的元素）
new Vue({
	data: function() {
		console.log(this)
	}
})
4. this的值? 答：new出来的对象

button.onclick = function() {
	this.disabled = true
	var btn = this
	$.get(‘/xxx.json’,function() {
		// this.disabled = false  // this是window
		btn.disabled = false
	})
}

因为this过于难用，提出了箭头函数，不再接受传参， 既没有this，也没有arguments
var f = ()=>{console.log(this)}  // 沿用的是外面的this
f.call(1) // window，不接受this的传值

内存机制可知：没有所谓的值传递，都是复制。
内存机制 https://juejin.cn/post/6844903615300108302? searchId=202309291636273017C406EC2F14788C74#heading-2
内存泄漏 https://www.ruanyifeng.com/blog/2017/04/memory-leak.html
js基本数据类型为什么可以调用方法 https://juejin.cn/post/6954336834210136094#heading-2




New 

思考：如何批量创建对象

注意 this，相当于js帮忙创建的空对象，最后又帮忙返回了这个对象



Js是动态语言，本身就是多态的
类就是构造函数（JAVA中不是这样）

JAVA中的类可以用一个关键字实现继承



单项绑定与双向绑定
没有组织的代码，意大利面条式代码
 
Web三层架构 

（设计模式：即套路）
MVC（借鉴后端的分类思想）
M model 专门负责数据 
V  view  专门负责表现
C  controller 负责其他逻辑

