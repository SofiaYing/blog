---
title: Hoisting
date: 2023-6-29
categories:
  - frond-end
tags :
  - base
---

## **What is Hoisting?**
During compile phase, just microseconds before your code is executed, it is scanned for function and variable declarations. All these functions and variable declarations are added to the memory inside a JavaScript data structure called Lexical Environment. So that they can be used even before they are actually declared in the source code.

The JavaScript engine is not physically moving your code, your code stays where you typed it.

All declarations (function, var, let, const and class) are hoisted in JavaScript, while the var declarations are initialized with undefined, but let and const declarations remain uninitialized.

[Lexical Enveronment](./executionContext.html)

在 JavaScript 中，所有绑定的声明会在控制流到达它们出现的作用域时被初始化；这里的作用域其实就是所谓的执行上下文（Execution Context），每个执行上下文分为内存分配（Memory Creation Phase）与执行（Execution）这两个阶段。在执行上下文的内存分配阶段会进行变量创建，即开始进入了变量的生命周期；

**变量的生命周期包含了三个过程：**
1. 声明/创建（Declaration phase）(creation phase)
2. 初始化（Initialization phase）
3. 赋值（Assignment phase）

## **Hoisting Function Declaration**
在编译阶段，函数声明形式的创建、初始化和赋值都提升了（编译阶段都已经写进此法环境中）

function declarations are added to the memory during the compile stage, so we are able to access it in our code before the actual function declaration.

So when the JavaScript engine encounters a call to helloWorld(), it will look into the lexical environment, finds the function and will be able to execute it.
```js
helloWorld()
function helloWorld() {
  console.log("Hello World!")
}

//词法环境
lexicalEnvironment = {
  helloWorld: < func >
}
```
::: tip Hoisting Function Expressions
此时helloWorld是一个变量，而非函数

helloWorld will be treated as a variable, not as a function. 
```js
var helloWorld = function() {}
```
:::

## **Hoisting var variables**
创建和初始化都提升了

When JavaScript engine finds a var variable declaration during the compile phase, it will add that variable to the lexical environment and initialize it with undefined and later during the execution when it reaches the line where the actual assignment is done in the code, it will assign that value to the variable.
```js
console.log(a) // undefined
var a = 3

// 变量环境中（与词法环境相同）
variableEnvironment = {
  a: undefined
}
```
### IIFE 立即执行函数
In the past, as there was only var, and it has no block-level visibility, programmers invented a way to emulate it. What they did was called “immediately-invoked function expressions” (abbreviated as IIFE).
立即执行函数IIFE Imdiately Invoked Function Expression

IIFE只有一个作用：创建一个独立的作用域。这个作用域里面的变量，外面访问不到（即避免了「变量污染」）

```js
//方法一
// Here, a Function Expression is created and immediately called. So the code executes right away and has its own private variables.
// 使用圆括号把该函数表达式包起来，以告诉 JavaScript，这个函数是在另一个表达式的上下文中创建的，因此它是一个函数表达式：它不需要函数名，可以立即调用。
(function() {
  var message = "Hello";
  console.log(message); // Hello
})();
//方法二
(function(){}())
//让Javascript引擎认为这是一个表达式的方法还有很多
!function(){}();
+function(){}();
-function(){}();
~function(){}();
new function(){ /* code */ }
new function(){ /* code */ }() // 只有传递参数时，才需要最后那个圆括号

// The Function Expression is wrapped with parenthesis (function {...}), because when JavaScript engine encounters "function" in the main code, it understands it as the start of a Function Declaration. But a Function Declaration must have a name, so this kind of code will give an error:
// 尝试声明并立即调用一个函数
function() { // <-- SyntaxError: Function statements require a function name (函数必须有名字)
  var message = "Hello";
}();
```
避免全局变量
```js
(function(win) {
    "use strict"; // 进一步避免创建全局变量，严格模式下你不能使用未声明的变量。
    var doc = win.document;
    // 在这里声明你的变量
    // 一些其他的代码
}(window));
```
## **Hoisting let varibales**
创建被提升
```js
console.log(a) // 暂时性死区 ReferenceError: Cannot access 'a' before initialization
let a = 3

lexicalEnvironment = {
  a: < uninitialized >
}
```

```js
let a // uninitialized
console.log(a) // initialize: undefined
a = 3
```
```js
          // 创建x
let x = 1 // 初始化为1
x = 2 // 赋值为2
```
### Temporal Dead Zone 暂时性死区
所谓暂时死区，就是不能在初始化之前，使用变量。
::: details
程序的控制流程在新的作用域进行实例化时，在此作用域中用let/const声明的变量会先在作用域中被创建出来，但因此时还未进行词法绑定（对声明语句进行求值运算），所以不能被访问（访问就会抛出错误）。所以在这运行流程进入作用域创建变量，到变量开始被访问之间的一段时间，就称之为TDZ（暂时性死区）

The variables are created when their containing Lexical Environment is instantiated but may not be accessed inany way until the variable’s  LexicalBinding is evaluated.
:::
```js
function foo () {
  console.log(a);
}
let a = 20;
foo();  // This is perfectly valid

function foo() {
 console.log(a); // ReferenceError
}
foo(); // This is not valid
let a = 20;
```
## **Hoisting const varibales**
const只有创建和初始化，没有赋值
const也有暂时性死区问题
```js
const x // Missing initializer in const declaration (const' declarations must be initialized.)

console.log(y) // 暂时性死区 Uncaught ReferenceError: Cannot access 'y' before initialization
const y = 1
y = 2 // Uncaught TypeError: Assignment to constant variable.
```
## **没有关键字时，不会提升，且为全局变量**
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
## **Hoisting Class Declaration**
创建提升，也有暂时性死区问题

Just as let and const declarations, classes in JavaScript are also hoisted, and just as let or const declarations, they remain uninitialized until evaluation. So they are also affected by the “Temporal Dead Zone”. 
```js
let peter = new Person('Peter', 25); // Uncaught ReferenceError: Cannot access 'Person' before initialization
console.log(peter);
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
```
## 思考题
```js
console.log(foo); // undefined
console.log(bar) // 函数多了一个赋值过程，var只有创建和提升（函数比var厉害）
var bar
function bar() {}
{
	console.log(foo); // function foo {...}
	foo = 1;
	function foo() {}
	foo = 2;
	console.log(foo); // 2 此时foo已经是函数的foo了
}
console.log(foo); // 1
```

## 参考文献
1. [Hoisting in Modern JavaScript — let, const, and var](https://blog.bitsrc.io/hoisting-in-modern-javascript-let-const-and-var-b290405adfda)
2. [我用了两个月的时间才理解 let](https://zhuanlan.zhihu.com/p/28140450)
3. [变量作用域与提升](https://juejin.cn/post/6844903490989342728)