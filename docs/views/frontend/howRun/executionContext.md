---
title: Execution Context
date: 2023-6-25
categories:
  - frond-end
tags :
  - base
---
## **JavaScript**
JavaScript, a high level, single-threaded, garbage collected, interpreted, or just-in-time compiled, prototype-based, multi-paradigm dynamic language with a non-blocking event loop made famous building websites.

It was created in 1995 in just one week by Brendan Ike with the goal of adding an easy to learn scripting language to the Netscape browser. It was originally named Mocha but the genius marketing people of the time wanted it to sound like that sexy new java language.

::: details an interpreted language
This means we do not have to compile the JavaScript source code before sending it to the browser. An interpreter can take the raw JavaScript code and run it for you.
:::
::: details a dynamically typed language
nlike C and C++. This means variables declared using var can store any type of data type like int, string, boolean and also complex data types like object and array.
The lack of type system is what makes JavaScript slow to run. A statically typed language can produce a much efficient machine code because of the information it has about the data like its type and size.
:::
### JavaScript Engine
- **Engine 引擎** 负责JavaScript程序的编译及执行过程。（compilation and execution phase）
::: details
Responsible for the compilation and execution of JavaScript programs.

The Engine consists of two main components:
* Memory Heap — this is where the memory allocation happens
* Call Stack — this is where your stack frames are as your code executes

Like any other programming language, JavaScript runtime has one stack and one heap storage. A heap is a free memory storage unit where you can store memory in random order. Data that is going to persist in for a considerable amount of time go inside the heap. Heap is managed by the JavaScript runtime and cleaned up by the garbage collector.

A stack is LIFO (last in, first out) data storage that stores the current function execution context of a program.  
:::
- **Compiler 编译器** 引擎的好朋友之一，负责语法分析（Parsing,生成抽象语法树AST Abstract Syntax Tree）及代码生成(Code Generation)等脏活累活。
::: details
There are various criteria for optimizing JavaScript code. Before JavaScript code is passed to the interpreter or baseline compiler, it has to first get parsed into an Abstract Syntax Tree (AST) which is a tree-like structure of the code.

When we run a JavaScript application, we do not need all the code at the application startup time. For example, if we have a function that is called on the user action, like a button click, that code can be parsed later.
:::
- **Scope 作用域** 引擎的另一位好朋友，负责收集并维护由所有声明的标识符（变量 varibale）组成的一系列查询(variable lookups)，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。
::: details
Scope in JavaScript refers to the accessibility /ək,sesə'biləti/（可访问性） or visibility of variables. That is, which parts of a program have access/ˈækses/ to the variable or where the variable is visible.
> <font color= #000>access</font>  *the right to enter a place, use something, see someone etc*
:::

比起需要三个步骤的传统编译语言（Tokenizing/Lexing 分词/词法分析；Parsing 解析/语法分析；Code Generation 代码生成），JavaScript引擎要复杂的多，大部分情况下编译发生在代码执行前的几微秒时间内，在所要讨论的作用域背后，JavaScript引擎用尽了各种办法来保证性能最佳。

## **Execution Context 执行上下文**
Simply put, an execution context is an abstract concept of an environment where the Javascript code is evaluated and executed. Whenever any code is run in JavaScript, it’s run inside an execution context.

ES3
- scope: 作用域，也常常被叫做作用域链（Scope Chain）
- variable object：变量对象，用于存储变量的对象
- this value：this值

ES5
- lexical environment: 词法环境，获取变量时使用
- variable enviroment: 变量环境，声明变量时使用
- this value

ES2018 （this值被归入lexical environment）
- lexical environment：词法环境，获取变量或this值时使用
- variable environment
- code evaluation state
- Function
- ScriptOrMoudle
- Realm
- Generator

### **Typeof of Execution Context 执行上下文类型**
JavaScript的可执行代码（executable code）只有三种：全局代码、函数代码、eval代码
- Global Execution Context
- Functional Execution Context
- Eval Function Execution Context

### **Execution Context Stack (ESC)/ Call Stack 调用栈**
Execution context stack, also known as “call stack” in other programming languages, is a stack with a LIFO (Last in, First out) structure, which is used to store all the execution context created during the code execution.

When the JavaScript engine first encounters your script, it creates a global execution context and pushes it to the current execution stack. Whenever the engine finds a function invocation, it creates a new execution context for that function and pushes it to the top of the stack.

The engine executes the function whose execution context is at the top of the stack. When this function completes, its execution stack is popped off from the stack, and the control reaches to the context below it in the current stack.

Each entry in the Call Stack is called a Stack Frame.

Once all the code is executed, the JavaScript engine removes the global execution context from the current stack.

## **How is the Execution Context created?**
The execution context is created in two phases: **1) Creation Phase(内存创建)** and **2) Execution Phase（代码执行）**.
### **Creation Phase**

  **Lexical Environment**

  Simply put, A lexical environment is a structure that holds identifier-variable mapping. (here identifier refers to the name of variables/functions, and the variable is the reference to actual object [including function object and array object] or primitive value).

  The lexical environment is basically data structure keeps the variable and their value reference in memory so that it can easily find it for execution context.

  **Each Lexical Environment has three components**
  - Environment Record
  ::: details
  The environment record is the place where the variable and function declarations are stored inside the lexical environment.
  :::
  - Reference to the outer envrionment 
  ::: details
  The reference to the outer environment means it has access to its outer lexical environment. That means that the JavaScript engine can look for variables inside the outer environment if they are not found in the current lexical environment.
  :::
  - This binding
  ::: details
  In this component, the value of this is determined or set.

  In the global execution context, the value of this refers to the global object. (in browsers, this refers to the Window Object).

  In the function execution context, the value of this depends on how the function is called. If it is called by an object reference, then the value of this is set to that object, otherwise, the value of this is set to the global object or undefined(in strict mode).
  :::

  **Variable Environment**  
  In ES6, one difference between LexicalEnvironment component and the VariableEnvironment component is that the former is used to store function declaration and variable (let and const) bindings, while the latter is used to store the variable (var) bindings only.

### **Execution Phase**

In this phase assignments to all those variables are done and the code is finally executed.

```js
let a = 20;
const b = 30;
var c;
function multiply(e, f) {
 var g = 20;
 return e * f * g;
}
c = multiply(20, 30);
```
When the above code is executed, the JavaScript engine creates a global execution context to execute the global code. 
* creation phase
```js
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      a: < uninitialized >, //the variables remain uninitialized (in case of let and const).
      b: < uninitialized >,
      multiply: < func > //the function declaration is stored in its entirety in the environment
    }
    outer: <null>,
    ThisBinding: <Global Object>
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      c: undefined, // the variables are initially set to undefined (in case of var) or remain uninitialized (in case of let and const).
    }
    outer: <null>,
    ThisBinding: <Global Object>
  }
}
```
* execution phase
```js
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {    
      Type: "Object",
      a: 20, //During the execution phase, if the JavaScript engine couldn’t find the value of let variable at the actual place it was declared in the source code, then it will assign it the value of undefined.
      b: 30,
      multiply: < func >
    }
    outer: <null>,
    ThisBinding: <Global Object>
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      c: undefined,
    }
    outer: <null>,
    ThisBinding: <Global Object>
  }
}
```
When a call to function multiply(20, 30) is encountered, a new function execution context is created to execute the function code. 
* creation phase
```js
FunctionExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      Arguments: {0: 20, 1: 30, length: 2},
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>,
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      g: undefined
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>
  }
}
```
* execution phase
```js
FunctionExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      Arguments: {0: 20, 1: 30, length: 2},
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>,
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      g: 20
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>
  }
}
```
After the function completes, the returned value is stored inside c. So the global lexical environment is updated. After that, the global code completes and the program finishes.
## 参考文献
1. [How does JavaScript and JavaScript engine work in the browser and node?](https://medium.com/jspoint/how-javascript-works-in-browser-and-node-ab7d0d09ac2f)
2. [Understanding Execution Context and Ececution Stack in JavaScript](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)
3. [JavaScript深入之执行上下文栈](https://github.com/mqyqingfeng/Blog/issues/4)