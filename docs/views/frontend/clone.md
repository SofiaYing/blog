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

引用类型 赋值和深/浅拷贝的区别
- 赋值：当我们把一个对象赋值给一个新的变量时，**赋的其实是该对象的在栈中的地址，而不是堆中的数据**。也就是两个对象指向的是同一个存储空间，无论哪个对象发生改变，其实都是改变的存储空间的内容，因此，两个对象是联动的。
- 浅拷贝：创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址 ，所以如果其中一个对象改变了这个地址，就会影响到另一个对象。
- 深拷贝：将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且修改新对象不会影响原对象。


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
### 方法一:JSON方法 JSON.parse(JSON.stringify(obj))
**局限**：
- 无法处理function、正则、日期。
- 拷贝其他引用类型、拷贝函数、循环引用等都是有问题的。

JSON.stringify() 将值转换为相应的JSON格式：
- 转换值如果有 toJSON() 方法，该方法定义什么值将被序列化。
- 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。
- 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值。
- undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 null（出现在数组中时）。函数、undefined 被单独转换时，会返回 undefined，如JSON.stringify(function(){}) or JSON.stringify(undefined).
- 对包含循环引用的对象（对象之间相互引用，形成无限循环）执行此方法，会抛出错误。
- 所有以 symbol 为属性键的属性都会被完全忽略掉，即便 replacer 参数中强制指定包含了它们。
- Date 日期调用了 toJSON() 将其转换为了 string 字符串（同Date.toISOString()），因此会被当做字符串处理。
- NaN 和 Infinity 格式的数值及 null 都会被当做 null。
- 其他类型的对象，包括 Map/Set/WeakMap/WeakSet，仅会序列化可枚举的属性。

```js
JSON.parse(JSON.stringify(obj))
```
### 方法二
深拷贝的问题可以分解为两个问题：浅拷贝+递归
深拷贝没有完美方案，每一种方案都有它的边界 case。而面试官向你发问也并非是要求你破解人类未解之谜，多数情况下，他只是希望考查你对递归的熟练程度。所以递归实现深拷贝的核心思路：
**写法一**
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
**写法二**
```js
function deepClone(target) {
  if (typeof traget === 'object') {
    let cloneTarget = {}
    for (const key in traget) {
      cloneTarget[key] = deepClone(target[key])
    }
    return cloneTarget
  } else {
    return target
  }
}
```
存在问题：
- 参数校验
- 是否为对象的判断逻辑不够严谨
- 数组的兼容问题?
- 递归爆栈问题
### 方法三 增加数组校验
```js
// 写法一
function deepClone(target) {
  if (typeof target === 'object') {
    let cloneTarget = Array.isArray(target)? []: {}
    for (const key in target) {
      cloneTarget[key] = deepClone(target[key])
    }
    return cloneTarget
  } else {
    return target
  }
}

// 写法二
function deepClone(source){
  //参数校验
  if (typeof(source) !== 'object' || source === null) {
    return source
  }
  let copy = {}
  //if(source instanceof(Array))
  if (source.constructor === Array) {
    copy = []
  }
  for (let key in source) {
    if (source.hasOwnProperty(key)) {
      copy[key] = sourcep[key]
    }
  }
  return copy
}
```
### 循环引用问题
**循环引用**：对象的属性间接或直接的引用了自身,如：
```js
let target = {
    field1: 1,
    field2: undefined,
    field3: 'ConardLi',
    field4: {
        child: 'child',
        child2: {
            child2: 'child2'
        }
    }
};
target.target = target
```
**引用丢失**：某些情况下可能想保留引用，如：
```js
var b = 1;
var a = {a1: b, a2: b};

a.a1 === a.a2 // true

var c = clone(a);
c.a1 === c.a2 // false
```
可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题。
```js
function deepClone(target, map = new Map()) {
  if (typeof target === 'object') {
    let cloneTarget = Array.isArray(target)? [] : {}
    if (map.get(target)) {
      return map.get(target)
    }
    map.set(target, cloneTarget)
    for (const key in target) {
      cloneTarget[key] = deepClone(target[key], map)
    }
    return cloneTarget
  } else {
    return target
  }
}
```
### 爆栈问题
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
[深拷贝的终极探索](https://segmentfault.com/a/1190000016672263)
[如何写出一个惊艳面试官的深拷贝?](https://juejin.cn/post/6844903929705136141)
[浅拷贝与深拷贝][https://juejin.cn/post/6844904197595332622)
[内功修炼之lodash—— clone&cloneDeep(一定有你遗漏的js基础知识)](https://juejin.cn/post/6844903999972474887#comment)
[lodash源码分析——deepclone](https://zhuanlan.zhihu.com/p/41699218)
[聊一聊JS中的循环引用及问题](https://zhuanlan.zhihu.com/p/102029144)
https://blog.csdn.net/qq_23853743/article/details/107622223