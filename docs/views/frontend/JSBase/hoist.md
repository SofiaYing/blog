---
title: Hoist
date: 2023-6-29
categories:
  - frond-end
tags :
  - base
---

```js
console.log(a) // Uncaught ReferenceError: a is not defined
console.log(b) // undefined
a = 1
var b = 2
function c() {
  var d = 3
  console.log(f) // Uncaught ReferenceError: f is not defined
  f = 4
  console.log(d , window.d, window.f, f)
}
console.log(f) // Uncaught ReferenceError: f is not defined
console.log(window.a, window.b, window.d, window.g) // 1, 2, undefined, undefined
c() // 3, undefined, 4, 4
```
```js
console.log(fn1) // ƒ fn1() { console.log('fn1') }
fn1() // fn1
function fn1() {
  console.log('fn1')
}
console.log(fn2) // undefined
fn2() //caught TypeError: fn2 is not a function
var fn2 = function() {
  console.log('fn2')
}
```

# Hoisting
JavaScript 中，所有绑定的声明会在控制流到达它们出现的作用域时被初始化；这里的作用域其实就是所谓的执行上下文（Execution Context），每个执行上下文分为内存分配（Memory Creation Phase）与执行（Execution）这两个阶段。在执行上下文的内存分配阶段会进行变量创建，即开始进入了变量的生命周期；变量的生命周期包含了声明（Declaration phase）、初始化（Initialization phase）与赋值（Assignment phase）过程这三个过程。

传统的 var 关键字声明的变量允许在声明之前使用，此时该变量被赋值为 undefined；而函数作用域中声明的函数同样可以在声明前使用，其函数体也被提升到了头部。这种特性表现也就是所谓的提升（Hoisting）；虽然在 ES6 中以 let 与 const 关键字声明的变量同样会在作用域头部被初始化，不过这些变量仅允许在实际声明之后使用。在作用域头部与变量实际声明处之间的区域就称为所谓的暂时死域（Temporal Dead Zone），TDZ 能够避免传统的提升引发的潜在问题。另一方面，由于 ES6 引入了块级作用域，在块级作用域中声明的函数会被提升到该作用域头部，即允许在实际声明前使用；而在部分实现中该函数同时被提升到了所处函数作用域的头部，不过此时被赋值为 undefined。


在JavaScript中，一个作用域(scope)中的名称(name)有以下四种：
- 语言自身定义(Language-defined): 所有的作用域默认都会包含this和arguments。
- 函数形参(Formal parameters): 函数有名字的形参会进入到函数体的作用域中。
- 函数声明(Function decalrations): 通过function foo() {}的形式。
- 变量声明(Variable declarations): 通过var foo;的形式。
补充说明：
- 一个名称进入一个作用域一共有四种方式。下面列出的顺序就是他们解析的顺序。
- 在作用域中重复地声明同名函数，则会由后者覆盖前者
- arguments 比较特殊，不遵守第二条规律，不建议用arguments作为变量名
```js
var a = 1
if(true){
  var a = 2
  console.log(a)  //2
}
console.log(a)  //2

//可以创建临时作用域 (与C系语言相似)
var a = 1
if(true){
  (function(){
    var a = 2
    console.log(a)  //2
  })()
}
console.log(a)  //1
```
避免全局变量
```js
(function(win) {
    "use strict"; // 进一步避免创建全局变量，严格模式下你不能使用未声明的变量。
    var doc = window.document;
    // 在这里声明你的变量
    // 一些其他的代码
}(window));
```

变量的生命周期:
- 变量声明（Declaration Phase）:在作用域中注册变量
- 变量初始化（Initialization Phase）:为变量分配内存并且创建作用域绑定，此时变量会被初始化为 undefined
- 变量赋值（Assignment Phase）:将开发者指定的值分配给该变量

- 变量提升只对 var 命令声明的变量有效，如果一个变量不是用 var 命令声明的，JavaScript 引擎就不会将其提升。
1. 创建和初始化(undefined)
2. 执行代码
3. 赋值
```js
console.log(b); //ReferenceError: b is not defined
b = 1; 
```
- 只有函数声明的方式会连函数体一起提升，而函数表达式中只会提升名称，函数体只有在执行到赋值语句时才会被赋值。
1. 创建、初始化、赋值一起完成
2. 执行代码
```js
  foo(); // TypeError "foo is not a function"
  bar(); // valid
  baz(); // TypeError "baz is not a function"
  spam(); // ReferenceError "spam is not defined"

  var foo = function () {}; // 匿名函数表达式('foo'被提升)
  function bar() {}; // 函数声明('bar'和函数体被提升)
  var baz = function spam() {}; // 命名函数表达式(只有'baz'被提升)

  foo(); // valid
  bar(); // valid
  baz(); // valid
  spam(); // ReferenceError "spam is not defined"
```
- const 只有创建和初始化，没有赋值过程
- let的提升和暂时性死区问题
1. 创建
2. 暂时性死区（let x 之前使用 x 会报错）
3. 初始化（(1)let x 初始化为undefined (2)let x=1 初始化为1）
4. 赋值
```js
//由于 function 比 var 多一个「赋值」过程，所以两个代码的输出都是函数。
var foo
function foo(){}
console.log(foo) //function

function foo(){}
var foo
console.log(foo) //function
```
结论：所谓暂时死区，就是不能在初始化之前，使用变量
- let 的「创建」过程被提升了，但是初始化没有提升。
- var 的「创建」和「初始化」都被提升了。
- function 的「创建」「初始化」和「赋值」都被提升了。

There are two main differences of var compared to let/const:
1. var variables have no block scope, their visibility is scoped to current function, or global, if declared outside function.
2. var declarations are processed at function start (script start for globals).
### this
在函数执行时，this 总是指向调用该函数的对象。要判断 this 的指向，其实就是判断 this 所在的函数属于谁。

### 闭包
当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。
```js
functin fn1(){
  var name = 'name'
  function fn2(){
    console.log(name)
  }
  return fn2
}
var fn3 = fn1()
fn3()  //name
```
执行fn3能正常输出name，即fn2能记住并访问它所在的词法作用域，而且fn2函数的运行还是在当前词法作用域之外了。
正常来说，当fn1函数执行完毕之后，其作用域是会被销毁的，然后垃圾回收器会释放那段内存空间。而闭包却很神奇的将fn1的作用域存活了下来，**fn2依然持有该作用域的引用，这个引用就是闭包。**
```js
function fn1() {
	var name = 'iceman';
	function fn2() {
		console.log(name);
	}
	fn3(fn2);
}
function fn3(fn) {
	fn();
}
fn1();
```
本例中，将内部函数fn2传递给fn3，当它在fn3中被运行时，它是可以访问到name变量的。
所以无论通过哪种方式将内部的函数传递到所在的词法作用域以外，它都会持有对原始作用域的引用，无论在何处执行这个函数都会使用闭包。

闭包的使用
```js
//定时器中有一个匿名函数，该匿名函数就有涵盖waitSomeTime函数作用域的闭包，因此当1秒之后，该匿名函数能输出msg。
function waitSomeTime(msg, time) {
	setTimeout(function () {
		console.log(msg)
	}, time);
}
waitSomeTime('hello', 1000);
```
另一个很经典的例子就是for循环中使用定时器延迟打印的问题
```js
for (var i = 1; i <= 10; i++) {
	setTimeout(function () {
		console.log(i);  //会输出10次11
	}, 1000);
}
```
究其原因：setTimeout中的匿名函数执行的时候，for循环都已经结束了，i是声明在全局作用中的，定时器中的匿名函数也是执行在全局作用域中，那当然是每次都输出11了。

解决方法：让i在每次迭代的时候，都产生一个私有的作用域，在这个私有的作用域中保存当前i的值。
```js
for (var i = 1; i <= 10; i++) {
	(function () {
		var j = i;
		setTimeout(function () {
			console.log(j);
		}, 1000);
	})();
}

for (var i = 1; i <= 10; i++) {
	(function (j) {
		setTimeout(function () {
			console.log(j);
		}, 1000);
	})(i);
}
```
闭包的应用
闭包的应用比较典型是定义模块，我们将操作函数暴露给外部，而细节隐藏在模块内部
```js
function module() {
	var arr = [];
	function add(val) {
		if (typeof val == 'number') {
			arr.push(val);
		}
	}
	function get(index) {
		if (index < arr.length) {
			return arr[index]
		} else {
			return null;
		}
	}
	return {
		add: add,
		get: get
	}
}
var mod1 = module();
mod1.add(1);
mod1.add(2);
mod1.add('xxx');
console.log(mod1.get(2));
```

### higher-order function
什么是高阶函数
至少满足以下条件的中的一个，就是高阶函数：
- 将其他函数作为参数传递（回调函数）：如AJAX callback(res),数据排序
- 将函数作为返回值
```js
function forEach(arr,callback){
  for(var i=0;i<arr.length;i++){
    callback(arr[i])
  }
}
```
```js
function before(func,n){
    let result
  if (typeof func !== 'function') {
    throw new TypeError('Expected a function')
  }
  return function(...args) {
    if (--n > 0) {
      result = func.apply(this, args)
    }
    if (n <= 1) {
      func = undefined
    }
    return result
  }
}
```

# English
**compilation:** Code compilation is a set of steps that process the text of your code and turn it into a list of instructions the computer can understand. Typically, the whole source code is transformed at once, and those resulting instructions are saved as output (usually in a file) that can later be executed.

**Interpretation** performs a similar task to compilation, in that it transforms your program into machine-understandable instructions. But the processing model is different. Unlike a program being compiled all at once, with interpretation the source code is transformed line by line; each line or statement is executed before immediately proceeding to processing the next line of the source code.
- porform *to do something, especially something difficult or useful*

编译和解释的过程上的区别：编译是将源程序翻译成可执行的目标代码，翻译与执行是分开的；而解释是对源程序的翻译与执行一次性完成，不生成可存储的目标代码。

编译和解释结果上的区别：编译的话会把输入的源程序翻译生成为目标代码，并存下来（无论是存在内存中还是磁盘上），后续执行可以复用；解释的话则是把源程序中的指令逐条解释，不生成也不存下目标代码，后续执行没有多少可复用的信息。

### IIFE
立即执行函数IIFE Imdiately Invoked Function Expression
IIFE只有一个作用：创建一个独立的作用域。这个作用域里面的变量，外面访问不到（即避免了「变量污染」）
这里有一个英语小知识
invoked 
1. 是形容词：立即调用的函数表达式
2. 实际上调用是被动的含义，要用的是过去分词，不叫过去式!
3. exciting news 现在分词做形容词，这个就表示令人兴奋的消息，是一个主动的意思
```js
//方法一
(function(){})()
//方法二
(function(){}())
//错误写法
function(){}
//让Javascript引擎认为这是一个表达式的方法还有很多
!function(){}();
+function(){}();
-function(){}();
~function(){}();
new function(){ /* code */ }
new function(){ /* code */ }() // 只有传递参数时，才需要最后那个圆括号
```

(变量作用域与提升)[https://juejin.cn/post/6844903490989342728]
(我用了两个月的时间才理解 let)[https://zhuanlan.zhihu.com/p/28140450]
(JavaScript开发者应懂的33个概念)[https://github.com/leonardomso/33-js-concepts]
(闭包详解一)[https://juejin.cn/post/6844903612879994887]
(闭包详解二：JavaScript中的高阶函数)[https://juejin.cn/post/6844903616885555214]
()[https://medium.com/sessionstack-blog/how-does-javascript-actually-work-part-1-b0bacc073cf]
[https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0]
(JS的执行上下文和作用域到底有什么区别？)[https://www.zhihu.com/question/482318118]