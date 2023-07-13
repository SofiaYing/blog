---
title: Closure
date: 2023-7-10
categories:
  - frond-end
tags :
  - base
---
## What is Closure?
「函数」和「函数内部能访问到的变量」的总和，就是一个闭包。

所以在《JavaScript权威指南》中就讲到：从技术的角度讲，所有的JavaScript函数都是闭包。

A closure is a way to access and manipulate external variables from within a function.

::: details 
A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives you access to an outer function's scope from an inner function. In JavaScript, closures are created every time a function is created, at function creation time. - MDN

A closure is a function that has access to its outer function scope even after the outer function has returned. This means a closure can remember and access variables and arguments of its outer function even after the function has finished.
:::

```js
// 一个闭包：foo 和可访问的外部变量 a
var a = 1
function foo() { 
  console.log(a)
}
```

闭包其实是 JS 函数作用域的副产品。是由于 JS 的函数内部可以使用函数外部的变量，所以这段代码正好符合了闭包的定义。而不是 JS 故意要使用闭包。很多编程语言也支持闭包，另外有一些语言则不支持闭包。
### What is a Lexical Scope?
A lexical scope or static scope in JavaScript refers to the accessibility of the variables, functions, and objects based on their physical location in the source code. 
[更多请查看Scope篇章](./scope.html)

## How do Closures Work?
```js
var a = 1
function foo() { 
  var b = 25
  console.log(a)
}
foo()
```
When the JavaScript engine creates a global execution context to execute global code, it also creates a new lexical environment to store the variables and functions defined in the global scope. 
```js
globalLexicalEnvironment = {
  environmentRecord: {
      a : 1,
      foo : < reference to function object >
  }
  outer: null
}
```
Here the outer lexical environment is set to null because there is no outer lexical environment for the global scope.

When the engine creates execution context for foo() function, it also creates a lexical environment to store variables defined in that function during execution of the function. 

The outer lexical environment of the function is set to the global lexical environment because the function is surrounded by the global scope in the source code.
```js
functionLexicalEnvironment = {
  environmentRecord: {
      b : 25,
  }
  outer: <globalLexicalEnvironment>
}
```
::: tip
When a function completes, its execution context is removed from the stack, but its lexical environment may or may not be removed from the memory depending on if that lexical environment is referenced by any other lexical environments in their outer lexical environment property.
:::

## 闭包的应用
### 使用闭包的目的——隐藏变量（函数嵌套的使用方法）
```js
function foo(){
  var local = 1
  function bar(){ //使用函数嵌套的方式使用闭包，是为了制造局部变量local，外部就无法直接修改local，只能通过间接的方式，从而达到隐藏变量的目的
    local++
    return local
  }
  return bar // return 是为了外部可以使用闭包，使用window.bar = bar 也是一样的效果
}

var func = foo()
func()
```
```js
!function(){ // 使用IIFE，与函数嵌套是一样的目的，为了隐藏变量
  var lives = 50
  window.addOneLive = function(){
    lives += 1
  }
  window.reduceOneLive = function(){
    lives -= 1
  }
}()
```
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
```js
function waitSomeTime(msg, time) {
	setTimeout(function () {
		console.log(msg)
	}, time);
}
waitSomeTime('hello', 1000);
```
```js
function showHelp(help) {
  document.getElementById('help').innerHTML = help;
}

function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];

  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i];
    document.getElementById(item.id).onfocus = function() {
      showHelp(item.help);
    }
  }
}

setupHelp();
```

```js
function getCounter() {
  let counter = 0;
  return function() {
    return counter++;
  }
}
let count = getCounter();
console.log(count());  // 0
console.log(count());  // 1
console.log(count());  // 2
```
Again we are storing the anonymous inner function returned by getCounter function into the count variable. As count function is now a closure, it can access the counter variable of getCounter function even after getCounter() has returned.

But notice that the value of the counter is not resetting to 0 on each count function call as it usually should.

That’s because, at each call of count(), a new scope for the function is created, but there is only single scope created for getCounter function, because the counter variable is defined in the scope of getCounter(), it would get incremented on each count function call instead of resetting to 0.

## **a tricky JavaScript Question**
```js
// interviewer: what will the following code output?
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  setTimeout(function() {
    console.log('Index: ' + i + ', element: ' + arr[i]);
  }, 3000);
}
// 4, undefined
// 4, undefined
// 4, undefined
// 4, undefined
```
The reason for this is because the setTimeout function creates a function (the closure) that has access to its outer scope, which is the loop that contains the index i. After 3 seconds go by, the function is executed and it prints out the value of i, which at the end of the loop is at 4 because it cycles through 0, 1, 2, 3, 4 and the loop finally stops at 4.arr[4] does not exist, which is why you get undefined.
::: details Why is this question so popular?
This question deals with the topics: closures, setTimeout, and scoping. 

This question tests your knowledge of some important JavaScript concepts, and because of how the JavaScript language works this is actually something that can come up quite often when you’re working — namely, needing to use setTimeout or some sort of async function within a loop.
:::
### solution 1
involves passing the needed parameters into the inner function
```js
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  (function(){
    var j = i
    setTimeout(function() {
      console.log('Index: ' + j + ', element: ' + arr[j]);
    }, 3000);
  })()
}
// 或
const arr = [10, 12, 15, 21];
for (var i = 0; i < arr.length; i++) {
  setTimeout(function(j) {
    return function() {
      console.log('Index: ' + j + ', element: ' + arr[j]);
    }
  }(i), 3000);
}
```
### solution 2
```js
const arr = [10, 12, 15, 21];
for (let i = 0; i < arr.length; i++) {
  // 1. for( let i = 0; i< 5; i++) 这句话的圆括号之间，有一个隐藏的作用域
  // 2. for( let i = 0; i< 5; i++) { 循环体 } 在每次执行循环体之前，JS 引擎会把 i 在循环体的上下文中重新声明及初始化一次。

  // 类似于 let i = 隐藏作用域中的i
  setTimeout(function() {
    console.log('Index: ' + i + ', element: ' + arr[i]);
  }, 3000);
}
```
**类似的问题**
```js
for (var i=0; i<10; i++) {
  document.getElementById(i).onclick = (function(x){
    return function(){
      alert(x);
    }
  })(i);
}
// 即
function generateMyHandler (x) {
  return function(){
    alert(x);
  }
}

for (var i=0; i<10; i++) {
  document.getElementById(i).onclick = generateMyHandler(i);
}
```
## 关于闭包的谣言——闭包会造成内存泄露？
这个谣言是如何来的？因为 IE。IE 有 bug，IE 在我们使用完闭包之后，依然回收不了闭包里面引用的变量。这是 IE 的问题，不是闭包的问题。参见司徒正美的[这篇文章](https://www.cnblogs.com/rubylouvre/p/3345294.html)。

## 参考文献
(闭包详解一)[https://juejin.cn/post/6844903612879994887]
(闭包详解二：JavaScript中的高阶函数)[https://juejin.cn/post/6844903616885555214]
1. [JS中的闭包是什么](https://zhuanlan.zhihu.com/p/22486908)
2. [Understanding Closures in JavaScript](https://blog.bitsrc.io/a-beginners-guide-to-closures-in-javascript-97d372284dda)
3. [A Tricky JavaScript Interview Question Asked by Google and Amazon](https://medium.com/coderbyte/a-tricky-javascript-interview-question-asked-by-google-and-amazon-48d212890703)