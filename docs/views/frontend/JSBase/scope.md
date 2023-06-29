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

## **Scope Chain**

## 参考文献
[Understanding Scope and Scope Chain in JavaScript](https://blog.bitsrc.io/understanding-scope-and-scope-chain-in-javascript-f6637978cf53)