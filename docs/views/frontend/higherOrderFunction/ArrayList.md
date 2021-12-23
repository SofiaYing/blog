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

### 方法二：Array.prototype.slice.call()/[].slice.call(arguments)
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
在函数调用时使用展开语法，等价于apply的方式。

### 方法四：Array.apply(null,arguments)
apply() 方法调用一个具有给定this值的函数，以及以一个**数组（或类数组对象）**的形式提供的参数。
数组/类数组中的元素将作为单独的参数传给函数。


## call/apply/bind
call/apply/bind的核心理念：**借用方法**。
借助已实现的方法，改变方法中数据的this指向，减少重复代码，节省内存。
### call/apply应用场景
1. 判断数据类型 `Object.prototype.toString.call(data)`
2. 类数组借用数组方法 `Array.prototype.push.call(arrayLike, add1)`
3. apply获取数组最大值最小值 `Math.max.apply(Math, arr)`(Math.max(-1, -3, -2);Math.max(...array))
4. 继承
```js
function Person(name) {
  this.name = name
  this.hobbies = ['swim', 'read']
}
Person.prototype.run = function() {console.log(this.name, '抬腿')}

function Student(name, age) {
  // 借用父类的方法：修改它的this指向,赋值父类的构造函数里面方法、属性到子类上
  Person.call(this, name)
  this.age = age
}
function inheritPrototype(subFn, supFn) {
  subFn.prototype = Object.create(supFn.prototype) // 继承父类的属性以及方法
  subFn.prototype.constructor = subFn // 修正constructor指向到继承的那个函数上
}
inheritPrototype(Student, Person)

Student.prototype.sayAge = function () {
    console.log(this.age, 'foo')
}
// 实例化
const ins1 = new Student('小明', 10)
const ins2 = new Student('小红', 12)
ins2.hobbies.push('football')
console.log(ins1) // {name: "小明", hobbies: ['swim', 'read'], age: 10}
console.log(ins2) // {name: "小红", hobbies: ['swim', 'read', 'football'], age: 12}
```
**call VS apply**
- 参数数量/顺序确定就用call，参数数量/顺序不确定的话就用apply。
- 考虑可读性：参数数量不多就用call，参数数量比较多的话，把参数整合成数组，使用apply。
- 参数集合已经是一个数组的情况，用apply，比如获取数组最大值/最小值。
### bind应用场景
1. 保存函数参数
https://juejin.cn/post/6844903621872582669
```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function(){
    console.log(i) // 6 6 6 6 6
  }, i* 1000)
}
// 解决方法: 闭包
for (var i = 1; i <= 5; i++) {
  (function(i){
    setTimeout(function(){
      console.log(i) // 1 2 3 4 5
    }, i* 1000)
  })(i)
}
// 解决方法：bind（bind会返回一个函数，这个函数也是闭包）
for (var i = 1; i <= 5; i++) {
  // 缓存参数
  setTimeout(function (i) {
      console.log('bind', i) // 1 2 3 4 5
  }.bind(null, i), i * 1000);
}
```
2. 回调函数this丢失问题
```js
class Page {
    constructor(callBack) {
        this.className = 'Page'
        this.MessageCallBack = callBack // 
        this.MessageCallBack('发给注册页面的信息') // 执行PageA的回调函数
    }
}
class PageA {
    constructor() {
        this.className = 'PageA'
        this.pageClass = new Page(this.handleMessage) // 注册页面 传递回调函数 问题在这里
    }
    // 与页面通信回调
    handleMessage(msg) {
        console.log('处理通信', this.className, msg) //  'Page' this指向错误
    }
}
new PageA() // 处理通信 Page 发给注册页面的信息
// 解决方案： bind
this.pageClass = new Page(this.handleMessage.bind(this)) // 绑定回调函数的this指向
// 解决方案： 箭头函数
this.pageClass = new Page(()=>{ this.handleMessage() }) // 箭头函数绑定this指向
```
### 手写call
在 function 函数运行时使用的 this 值。请注意，this可能不是该方法看到的实际值：如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象，原始值会被包装。
```js
Function.prototype.myCall = function(context, ...arr) {
  if (context === null || context === undefined) {
     // 指定为 null 和 undefined 的 this 值会自动指向全局对象(浏览器中为window)
    context = window
  } else {
    context = Object(context) // 值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的实例对象
  }

  const fn = Symbol(); // 防止属性污染，确保不会出现同名属性
  context[fn] = this // 把方法赋值为调用它的函数
  let result = context[fn](...arr); // 此时this已经是context
  delete context[fn]

  return result
}

// 使用
function test(age) {
    console.log(this.name + " " + age);
}
var obj = {
    name: 'Jack'
}
test.myCall(obj, 22) // Jack 22
```


[js基础-面试官想知道你有多理解call,apply,bind？[不看后悔系列]](https://juejin.cn/post/6844903906279964686)
[死磕 36 个 JS 手写题（搞懂后，提升真的大）](https://juejin.cn/post/6946022649768181774#heading-27)