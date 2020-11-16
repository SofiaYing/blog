---
title: 观察者模式
date: 2020-11-16
categories:
  - frond-end
tags :
  - design patterns
---

## 观察者模式（Observer Pattern） 
发布/订阅模式（Publish Subscribe Pattern）

> 观察者模式定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个目标对象，当这个目标对象的状态发生变化时，会通知所有观察者对象，使它们能够自动更新。 —— Graphic Design Patterns

角色划分 --> 状态变化 --> 发布者通知到订阅者
* 两个关键角色——发布者和订阅者。用面向对象的方式表达的话，那就是要有两个类。
* 发布者(publisher) 基本功能: 增加订阅者、通知订阅者、移除订阅者
* 订阅者(observer) 基本功能：被通知、去执行
```javascript
class Publisher {
  constructor() {
    this.observers = []
  }
  //增加订阅者
  add(observer) {
    this.observers.push(observer)
  }
  //通知订阅者
  notify() {
    this.observers.forEach((observer) => {
      observer.update(this)
    })
  }
  //移除订阅者
  remover(observer) {
    this.observer.forEach((item,index) => {
      if (item === observer) {
        this.observers.splice(index,1)
      }
    })
  }
}
```
```javascript
class Observer {
  constructor() {}
  update() {}
}
```
## 观察者模式与发布-订阅模式的区别是什么？
* 发布者直接触及到订阅者的操作，叫观察者模式。[示例：计时器](#observer)
* 发布者不直接触及到订阅者、而是由统一的第三方来完成实际的通信的操作，叫做发布-订阅模式。[示例：Event Bus/Event Emitter](#pub)

即观察者模式和发布-订阅模式之间的区别，在于是否存在第三方、发布者能否直接感知订阅者。

## Vue数据双向绑定（响应式系统）的实现原理

<div id="pub"></div>

## 实现一个Event Bus/ Event Emitter

<div id="observer"></div>

## 实例：计时器
实例中涉及到的知识点：
1. prototype、类
2. ES6 解构、模板字符串
3. Array高阶函数
4. String indexOF
5. requestAnimationFrame
6. 立即执行函数
7. 闭包
8. 事件循环机制
```javascript
// 场景：页面内可能同时存在多个计时器，可能导致计时器不准确。
function Timer(){
  this._currentTime = Data.now()
  this._passTime = 0
  this.observers = []
  this._timerId = null
}

Timer.prototype.attach = function(observer) {
  let obj = {
    key : `${observer.id}_key`,
    target: obsserver
  }
  this.observers.push(obj)
}

Timer.prototype.start = function() {
      function timimg() {
        let _nowTime = Date.now()
        this._passTime += _nowTime - this._currentTime
        this._currentTime = _nowTime
        this.notifyObserver()
        window.requestAnim(timing.bind(this))
      }
      if (this._timerId) {
        window.cancelAnim(this._timerId)
      }
      this._timerId = window.requestAnim(timing.bind(this))
}

Timer.prototype.stop = function() {
  this.observers = []
  this._timerId && window.cancelAnim(this._timerId)
}

Timer.prototype.notifyObserver = function() {
  for (const { key, target } of this.observer) {
    let result = target.update(this._passTime)
    if (result) {
      deleteKeys += `${key}`
    }
  }
  if (deleteKeys) {
    this.observers = this.observers.filter(({ key }) => {
      return deleteKeys.indexOf(key) < 0
    })
  }
}

window.requestAnim = (function() {
  return window.requestAnimationFrame ||
    window.webkitRequestAnimationFrame ||
    window.mozRequestAnimationFrame ||
    window.oRequestAnimationFrame ||
    window.msRequestAnimationFrame ||
    function(callback) {
      window.setTimeout(callback, 1000 / 60)
    }
})()

window.cancelAnim = (function(){
  return window.cancelAnimationFrame || 
    window.mozCancelAnimationFrame ||
    function(id) {
      window.clearTimeout(id)
    }
}())
```

参考文献
1. [JavaScript 设计模式核⼼原理与应⽤实践](https://juejin.im/book/6844733790204461070/section/6844733790279958535)
2. [倒计时的那些坑](https://juejin.im/post/6844904121963642894#heading-5)
