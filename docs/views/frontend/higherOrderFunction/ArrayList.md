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
处于ECMAScript5严格模式下，call和apply第一个参数都会变为this；在ECMAScript3和非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象，原始值会被包装。

- call()方法的作用和 apply() 方法类似，区别就是call()方法接受的是参数列表，而apply()方法接受的是一个参数数组。
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
处于ECMAScript5严格模式下，call和apply第一个参数都会变为this；在ECMAScript3和非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象，原始值会被包装。
```js
Function.prototype.myCall = function(context, ...arr) {
  if (context === null || context === undefined) {
     // 指定为 null 和 undefined 的 this 值会自动指向全局对象(浏览器中为window)
     // 不能简单的以context是否为false，因为“”，0，false也会判定为false
    context = window
  } else {
    context = Object(context) // 值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的实例对象
  }

  const fn = Symbol(); // 防止属性污染，确保不会出现同名属性
  context[fn] = this // 把方法赋值为调用它的函数（this）
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

**思路如下：**
```js
var foo = {
  value: 1
}
function bar() {
  console.log(this.value)
}

boo.call(foo) // 1
```
1. 然后改变this的指向
```js
var foo = {
  value: 1,
  bar: function() {
    console.log(this.value)
  }
}
foo.bar() //此时this的指向被改变了
// 实现步骤：
// 1. 将函数设为对象的属性
// 2. 执行该函数
// 3. 删除该函数
// foo.call(bar)
Function.prototype.myCall = function(context) {
  //ES6
  fn = new Symbol()
  
  context[fn] = this
  let result = context[fn]()
  delete context[fn]
  return result
}
```
2. fn同名覆盖问题
- ES6 Symbol
- ES3 Math.random / 时间戳
```js
// 时间戳方法
'_'+ new Date().getTime()
// Math.random方法
function generateUUID() {
  var i, random
  var uuid = ''
  for(i = 0;i<32;i++) {
    random = Math.random()*16 | 0
    if (i === 8 || i === 12 || i === 16 || i === 20) {
        uuid += '-';
    }
    uuid += (i === 12 ? 4 : (i === 16 ? (random & 3 | 8) : random))
        .toString(16);
  }
  return uuid
}
```
进一步可以增加缓存操作
```js
var student = {
    name: 'Jack',
    doSth: 'doSth',
};
var originalVal = student.doSth;
var hasOriginalVal = student.hasOwnProperty('doSth');
student.doSth = function(){};
delete student.doSth;
// 如果没有，`originalVal`则为undefined，直接赋值新增了一个undefined，这是不对的，所以需判断一下。
if(hasOriginalVal){
    student.doSth = originalVal;
}
```
3. 传参
**ES3 eval**
```js
Function.prototype.myCall = function(context) {
  fn = new Symbol()
  context[fn] = this
  var args = []
  for(var i=1;i<arguments.length;i++) {
    args.push('arguments['+i+']') //拼成参数字符串 args = [arguments[1], arguments[2], ...]
  }
  // 1.eval 将传入的字符串当做 JavaScript 代码进行执行
  // 2.在eval中，args 自动调用 args.toString()方法
  // 3.相当于context.fn(arguments[1], arguments[2], ...)
  var result = eval('context.fn(' + args +')') 

  delete context[fn]
  return result
}
```
4. 条件判断
```js
if (context === null || context === undefined) {
    // 指定为 null 和 undefined 的 this 值会自动指向全局对象(浏览器中为window)
    // 不能简单的以context是否为false，因为“”，0，false也会判定为false
  context = window
} else {
  context = Object(context) // 值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的实例对象
}
```

### 手写apply
```js
// 浏览器环境 非严格模式 返回全局对象
function getGlobalObject(){
    return this;
}

Function.prototype.myApply = function(context) {
  // 如果不是函数，抛出错误
  if(typeof this !== 'function'){
    throw new TypeError(this + ' is not a function');
  }

  if ( context === undefined || context === null) {
    // context = window
    context = getGlobalObject()
  } else {
    context = Object(context)
  }

  function isArrayLike(o) {
    if ( o && typeof o === 'object' && isFinite(o.length) &&)
  }

  const fn = Symbol('special')
  context[fn] = this
  
  let args = arguments[1]
  let result

  // // 如果 argArray 是 null 或 undefined, 则返回提供 thisArg 作为 this 值并以空参数列表调用 func 的 [[Call]] 内部方法的结果。
  // if(typeof argsArray === 'undefined' || argsArray === null){
  //     argsArray = [];
  // }
  // // 3.如果 Type(argArray) 不是 Object, 则抛出一个 TypeError 异常 .
  // if(argsArray !== new Object(argsArray)){
  //     throw new TypeError('CreateListFromArrayLike called on non-object');
  // }

  if(args) {
    if (!Array.isArray(args) && !isArrayLike(args)) {
      throw new TypeError('myApply 第二个参数不为数组并且不为类数组对象抛出错误');
    } else {
      args = Array.from(args) // 转为数组
      result = context[fn](...args) // 执行函数并展开数组，传递函数参数
    }
  } else {
    result = context[fn]()
  }

  delete context[fn]
  return result
}

```
关于传参
**eval**
```js
Function.prototype.apply = function (context, arr) {
    var context = Object(context) || window;
    context.fn = this;

    var result;
    if (!arr) {
        result = context.fn();
    }
    else {
        var args = [];
        for (var i = 0, len = arr.length; i < len; i++) {
            args.push('arr[' + i + ']');
        }
        result = eval('context.fn(' + args + ')')
    }

    delete context.fn
    return result;
}
```
**new Function**
创建函数`new Function ([arg1[, arg2[, ...argN]],] functionBody)`
如 `new Function('a', 'b', 'return a + b');`
```js
var student = {
    name: 'Jack',
    doSth: function(argsArray){
        console.log(argsArray);
        console.log(this.name);
    }
};
var result = student.doSth(['Rowboat', 18]);

// 可以写成如下方式
var result = new Function('return arguments[0][arguments[1]](arguments[2][0], arguments[2][1])')(student, 'doSth', ['Rowboat', 18]);
// 相当于创建一个匿名函数 
// function(){
//  arguments[0][arguments[1]](arguments[2][0], arguments[2][1])
// }
// 传入参数 student, 'doSth', ['Rowboat', 18]

// 个数不定的情况
function generateFunctionCode(argsArrayLength) {
  var code = 'return arguments[0][arguments[1]](';
  for(var i = 0; i < argsArrayLength; i++){
      if(i > 0){
          code += ',';
      }
      code += 'arguments[2][' + i + ']';
  }
  code += ')';

  // return arguments[0][arguments[1]](arg1, arg2, arg3...)
  return code
}
```
```js
// 浏览器环境 非严格模式
function getGlobalObject(){
    return this;
}
function generateFunctionCode(argsArrayLength){
    var code = 'return arguments[0][arguments[1]](';
    for(var i = 0; i < argsArrayLength; i++){
        if(i > 0){
            code += ',';
        }
        code += 'arguments[2][' + i + ']';
    }
    code += ')';
    // return arguments[0][arguments[1]](arg1, arg2, arg3...)
    return code;
}
Function.prototype.applyFn = function apply(thisArg, argsArray){ // `apply` 方法的 `length` 属性是 `2`。
    // 1.如果 `IsCallable(func)` 是 `false`, 则抛出一个 `TypeError` 异常。
    if(typeof this !== 'function'){
        throw new TypeError(this + ' is not a function');
    }
    // 2.如果 argArray 是 null 或 undefined, 则
    // 返回提供 thisArg 作为 this 值并以空参数列表调用 func 的 [[Call]] 内部方法的结果。
    if(typeof argsArray === 'undefined' || argsArray === null){
        argsArray = [];
    }
    // 3.如果 Type(argArray) 不是 Object, 则抛出一个 TypeError 异常 .
    if(argsArray !== new Object(argsArray)){
        throw new TypeError('CreateListFromArrayLike called on non-object');
    }
    if(typeof thisArg === 'undefined' || thisArg === null){
        // 在外面传入的 thisArg 值会修改并成为 this 值。
        // ES3: thisArg 是 undefined 或 null 时它会被替换成全局对象 浏览器里是window
        thisArg = getGlobalObject();
    }
    // ES3: 所有其他值会被应用 ToObject 并将结果作为 this 值，这是第三版引入的更改。
    thisArg = new Object(thisArg);
    var __fn = '__' + new Date().getTime();
    // 万一还是有 先存储一份，删除后，再恢复该值
    var originalVal = thisArg[__fn];
    // 是否有原始值
    var hasOriginalVal = thisArg.hasOwnProperty(__fn);
    thisArg[__fn] = this;
    // 9.提供 `thisArg` 作为 `this` 值并以 `argList` 作为参数列表，调用 `func` 的 `[[Call]]` 内部方法，返回结果。
    // ES6版
    // var result = thisArg[__fn](...args);
    var code = generateFunctionCode(argsArray.length);
    var result = (new Function(code))(thisArg, __fn, argsArray);
    delete thisArg[__fn];
    if(hasOriginalVal){
        thisArg[__fn] = originalVal;
    }
    return result;
};
```
### 利用apply实现call
```js
Function.prototype.callFn = function call(thisArg){
    var argsArray = [];
    var argumentsLength = arguments.length;
    for(var i = 0; i < argumentsLength - 1; i++){
        // 事实上push方法，内部也有一层循环。所以理论上不使用push性能会更好些。
        // argsArray.push(arguments[i + 1]); 
        argsArray[i] = arguments[i + 1];
    }
    console.log('argsArray:', argsArray);
    return this.applyFn(thisArg, argsArray);
}
```
注：非严格模式下，thisArg原始值会包装成对象，添加函数并执行，再删除。而严格模式下还是原始值这个没有实现，而且万一这个对象是冻结对象呢，Object.freeze({})，是无法在这个对象上添加属性的。所以这个方法只能算是非严格模式下的简版实现。

### 手写bind
bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。
思路
```js
var foo = {
  value: 1
}
function bar() {
  console.log(this.value)
}

var bindFoo = bar.bind(foo) 
bindFoo() // 1
```
[JavaScript深入之call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)
[面试官问：能否模拟实现JS的call和apply方法](https://juejin.cn/post/6844903728147857415)
[js基础-面试官想知道你有多理解call,apply,bind？[不看后悔系列]](https://juejin.cn/post/6844903906279964686)
[死磕 36 个 JS 手写题（搞懂后，提升真的大）](https://juejin.cn/post/6946022649768181774#heading-27)