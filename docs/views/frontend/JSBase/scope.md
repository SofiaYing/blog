---
title: Scope
date: 2023-6-29
categories:
  - frond-end
tags :
  - base
---
## **Scope**
作用域：程序源代码中定义变量的区域，规定了如何查找变量，也就是确定当前执行代码对变量的访问权限。

Scope in JavaScript refers to the accessibility or visibility of variables. That is, which parts of a program have access to the variable or where the variable is visible.

### **Why is Scope Important?**
1. 代码安全性 The main benefit of scope is security. That is, the variables can be accessed from only a certain area of the program. Using scope, we can avoid unintended modifications to the variables from other parts of the program.
2. 命名冲突 The scope also reduces the namespace collisions. That is, we can use the same variable names in different scopes.

### **Types of Scope**
There are three types of scope in JavaScript — **1) Global Scope**, **2) Function Scope**, and, **3) Block Scope**.

1. **Global Scope 全局作用域**
Any variable that’s not inside any function or block (a pair of curly braces), is inside the global scope. The variables in global scope can be accessed from anywhere in the program. 
2. **Local Scope or Function Scope 函数作用域**
Variables declared inside a function is inside the local scope. They can only be accessed from within that function, that means they can’t be accessed from the outside code. 
3. **Block Scope 块级作用域**
ES6 introduced let and const variables, unlike var variables, they can be scoped to the nearest pair of curly braces 大括号. That means, they can’t be accessed from outside that pair of curly braces. 

## **Lexical Scope or Static Scope 词法作用域/静态作用域**
- 词法作用域/静态作用域：函数的作用域在函数定义的时候就决定了。
- 动态作用域：函数的作用域是在函数调用的时候才决定的。

词法作用域：词法作用域是 JavaScript 闭包特性的重要保证。一般来说，在编程语言里我们常见的变量作用域就是词法作用域与动态作用域（Dynamic Scope），绝大部分的编程语言都是使用的词法作用域。词法作用域注重的是所谓的 Write-Time，即编程时的上下文，而动态作用域以及常见的 this 的用法，都是 Run-Time，即运行时上下文。词法作用域关注的是函数在何处被定义，而动态作用域关注的是函数在何处被调用。JavaScript 是典型的词法作用域的语言,编译阶段就能够知道全部标识符在哪里以及是如何声明的，所以词法作用域是静态的作用域，也就是词法作用域能够预测在执行代码的过程中如何查找标识符。

Lexical Scope (also known as Static Scope) literally means that scope is determined at the lexing time (generally referred to as compiling) rather than at runtime.

Using lexical scope we can determine the scope of the variable just by looking at the source code. Whereas in the case of dynamic scoping the scope can’t be determined until the code is executed.

```js
var value = 1
function foo() {
  console.log(value)
}
function bar() {
  var value = 2
  foo()
}
bar()
// 1 in Lexical Scope
// 2 in Dynamic Scope
```

### **Lexical Environment vs. Lexical Scope**
Lexical scope is a scope that is determined at compile time and a lexical environment is a place where variables are stored during the program execution.

A new lexical environment is created for each lexical scope but only when the code in that scope is executed. The lexical environment also has a reference to its outer lexical environment

- Lexical Environment

A lexical environment is a structure that holds identifier-variable mapping. (here identifier refers to the name of variables/functions, and the variable is the reference to actual object [including function object and array object] or primitive value).

Simply put, a lexical environment is a place where variables and references to the objects are stored.
```js
lexicalEnvironment = {
  a: 25,
  obj: <ref. to the object>,
  outer: <outer lexical environemt>
}
```

## **Scope Chain**
当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。

When a variable is used in JavaScript, the JavaScript engine will try to find the variable’s value in the current scope. If it could not find the variable, it will look into the outer scope and will continue to do so until it finds the variable or reaches global scope.

If it’s still could not find the variable, it will either implicitly declare the variable in the global scope (if not in strict mode) or return an error.

### **How Does JavaScript Engine Perform Variable Loopups?**
Now that we know what scope, scope chain and lexical environment are, let’s now understand how JavaScript engine uses the lexical environment to determine scope and scope chain.

> <font color= #000>perform</font>  *to do something, especially something difficult or useful*

```js
let greeting = 'Hello';
function greet() { // greet
  let name = 'Peter';
  console.log(`${greeting} ${name}`);
}
greet();
{
  let greeting = 'Hello World!'
}
```
When the above script loads, a global lexical environment is created, which contains variables and functions defined inside the global scope. 
```js
globalLexicalEnvironment = {
  greeting: 'Hello'
  greet: <ref. to greet function> // greet函数被创建，保存作用域链到 内部属性. (greetScope.[[scope]] = globalContext.varibaleObject)
  outer: <null> // Here outer lexical environment is set to null because there is no outer scope after global scope.
}
```
After that, a call to the greet() function is encountered. So a new lexical environment is created for the greet() function. 
```js
// 执行 greet 函数，创建 greet 函数执行上下文，checkscope 函数执行上下文被压入执行上下文栈
// greet 函数并不立刻执行，开始做准备工作，第一步：复制函数[[scope]]属性创建作用域链
// 用 arguments 创建活动对象，随后初始化活动对象，加入形参、函数声明、变量声明
// 将活动对象压入 greet 作用域链顶端
functionLexicalEnvironment = {
  name: 'Peter',
  outer: <globalLexicalEnvironment> // Here outer lexical environment is set to globalLexicalEnvironment because its outer scope is the global scope.
}
```
After that, the JavaScript engine executes the console.log(`${greeting} ${name}`) statement.
::: details
The JavaScript engine tries to find the greeting and name variables inside the function’s lexical environment. It finds the name variable inside the current lexical environment but it won’t be able to find the greeting variable inside the current lexical environment.

So it looks inside the outer lexical environment (defined by the outer property i.e global lexical environment) and finds the greeting variable.
::: tip
  A new lexical environment is created only for let and const declarations, not var declarations. var declarations are added to the current lexical environment (global or function lexical environment).
:::

Next, the JavaScript engine executes at the code inside the block. So it creates a new lexical environment for that block.
```js
blockLexicalEnvironment = {
  greeting: 'Hello World',
  outer: <globalLexicalEnvironment>
}
```
So in a nutshell, a scope is an area where a variable is visible and accessible. Just like functions, scopes in JavaScript can be nested and the JavaScript engine traverses the scope chain to find the variables used in the program.

JavaScript uses lexical scope which means that scope of variables is determined at compile time. The JavaScript engine uses the lexical environment to store the variables during the program execution.

>
**补充：关于ES3的scopeChain是怎么产生的？**
1. [Execution context, Scope chain and JavaScript internals](https://medium.com/@happymishra66/execution-context-in-javascript-319dd72e8e2c)
2. [JavaScript深入之作用域链](https://github.com/mqyqingfeng/Blog/issues/6)

## 参考文献
1. [Understanding Scope and Scope Chain in JavaScript](https://blog.bitsrc.io/understanding-scope-and-scope-chain-in-javascript-f6637978cf53)
