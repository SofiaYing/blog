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