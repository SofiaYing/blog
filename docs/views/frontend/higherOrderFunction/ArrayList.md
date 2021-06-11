# ArrayList
`typeof(ArrayList)  // object`
使用数字作为属性名称(有索引),具备length属性
- 理论上可以将数组才具有的push,slice 等方法都加到类数组上面。但是这样为什么不直接使用数组就好了。其实恰恰相反，类数组不需要数组的那些方法，有了反而会画蛇添足。类数组对象的设计目的更多是只让你遍历和访问下标,而不是去添加或删除元素。

如
```js
const arr = {
  0:'张三',
  1:'李四',
  length:2
}
```
再如 arguments:如果 arguments 也有 push方法，那么arguments 就容易被开发者随意篡改。
![arguments](../images/arguments)

(JavaScript 为什么需要类数组)[https://zhuanlan.zhihu.com/p/214337851]

## 转数组
### 方法一：Array.from
类数组 以及 可迭代的对象 String
```js
Array.from('一二三四五六七') // ["一", "二", "三", "四", "五", "六", "七"] // 等效的es5是'一二三四五六七'.split('')
Array.from(new Set([1,2,1,2])) // 等效[...new Set([1,2,1,2])] => [1,2] // 用来数组去重
Array.from(new Map([[1, 2], [2, 4], [4, 8]])) // 接受一个map
Array.from(arguments)// 接受一个类数组对象
Array.from(document.querySelectorAll('div')) Array.from({1: 2,length:3}) // [undefined, 2, undefined]
```
!(Array.from() 五个超好用的用途)[https://juejin.cn/post/6844903926823649293]

### 方法二：Array.prototype.slice.call()
- slice()方法可从已有的数组中返回选定的元素。
- slice()方法可提取字符串的某个部分，并以新的字符串返回被提取的部分。
- 注意： slice() 方法不会改变原始数组。

**为什么要用call?**
举例，arguments转为数组：arguments是类数组，不能直接使用Array方法，并需要将方法内的this，重新指向arguments
```js
function(){
  Array.prototype.slice.call(arguments)
}

Array.prototype.slice = function(start,end){
  //代码内部使用了this
  var result = new Array()
}
[1,2,3].slice(1) //[2,3]
```
### 方法三：扩展运算符（展开运算符）

### 方法四：Array.apply(null,arguments)
apply() 方法调用一个具有给定this值的函数，以及以一个数组（或类数组对象）的形式提供的参数。
在函数调用时使用展开语法，等价于apply的方式

**call VS apply**
call()方法的作用和 apply() 方法类似，区别就是call()方法接受的是参数列表，而apply()方法接受的是一个参数数组。
