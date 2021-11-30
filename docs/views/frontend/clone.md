---
title: 深拷贝&浅拷贝
date: 2020-12-23
categories:
  - frond-end
tags :
  - base
---

“模拟 JAVA 中的克隆接口”
“JavaScript 实现原型模式”
“请实现JS中的深拷贝”

## 浅拷贝 shallow clone
浅拷贝仅拷贝一层
```js
function shallowClone(source){
  var target = {}
  for(var key in source){
    if(source.hasOwnProperty(key)){
      target[key] = source[i]
    }
  }
  return target
}
```
## 深拷贝 deep clone

方法一:JSON方法
局限：无法处理function、正则、日期

JSON对值的规定
复合类型的值只能是数组或对象，不能是函数、正则表达式对象、日期对象。
原始类型的值只有四种：字符串、数值（必须以十进制表示）、布尔值和null（不能使用NaN, Infinity, -Infinity和undefined）。
字符串必须使用双引号表示，不能使用单引号。
对象的键名必须放在双引号里面。
数组或对象最后一个成员的后面，不能加逗号。
```js
JSON.parse(JSON.stringify(obj))
```
深拷贝的问题可以分解为两个问题：浅拷贝+递归
深拷贝没有完美方案，每一种方案都有它的边界 case。而面试官向你发问也并非是要求你破解人类未解之谜，多数情况下，他只是希望考查你对递归的熟练程度。所以递归实现深拷贝的核心思路：
```js
function deepClone(source){
  var target = {}
  for(var key in source){
    if(source.hasOwnProperty(key)){
      if(typeof(source[key]) === 'object'){
        target[key] = deepClone(source[key])
      }else{
        target[key] = source[key]
      }
    }
  }
  return target
}
```
存在问题：
- 参数校验
- 是否为对象的判断逻辑不够严谨
- 数组的兼容问题?
- 递归爆栈问题
```js
function deepClone(source){
  //参数校验
  if(typeof(source) !== 'object' || source === null){
    return source
  }
  let copy = {}
  //if(source instanceof(Array))
  if(source.constructor === Array){
    copy = []
  }
  for(let key in source){
    if(source.hasOwnProperty(key)){
      copy[key] = sourcep[key]
    }
  }
  return copy
}
```
```js
//解决爆栈问题：循环遍历
function cloneLoop(data){
  const root = {}

  const loopStack = [
    {
      parent: root,
      key: undefined,
      data: data
    }
  ]

  while(loopStack.length){
    const node = loopList.pop()
    const parent = node.parent
    const key = node.key
    const data = node.data

    let res = parent
    if(typeof(key) !== 'undefined'){
      res = parent[key] = {}
    }

    for(let k in data){
      if(data.hasOwnProperty(k)){
        if(typeof(k) === 'objeck' && k!==null){
          loopStack.push({
            parent:res,
            key:k,
            data:data[k]
          })
        }else{
          res[k] = data[k]
        }
      }
    }
  }

  return root
}
```
深拷贝：https://segmentfault.com/a/1190000016672263
https://blog.csdn.net/qq_23853743/article/details/107622223

