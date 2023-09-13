---
title: Asynchronous
date: 2023-9-1
categories:
  - frond-end
tags :
  - asynchronous
---
JS使用基于事件循环的单线程执行方式，而且是非抢断执行的（也就是说，无论发生什么，都会把当前任务执行完，不会出现执行到一半就去执行别的任务的情况）。

JS的单线程事件循环很适合前端使用，大幅简化了程序的复杂度，同时前端少会有大型计算任务，所以性能也并非问题。

我们都知道 Js 是单线程都，但是一些高耗时操作就带来了进程阻塞问题。为了解决这个问题，Js 有两种任务的执行模式：同步模式（Synchronous）和异步模式（Asynchronous）。

在异步模式下，创建异步任务主要分为宏任务与微任务两种。ES6 规范中，宏任务（Macrotask） 称为 Task， 微任务（Microtask） 称为 Jobs。宏任务是由宿主（浏览器、Node）发起的，而微任务由 JS 自身发起。

::: details Asynchronous
JavaScript is a single-threaded programming language which means only one thing can happen at a time. That is, the JavaScript engine can only process one statement at a time in a single thread.

While the single-threaded languages simplify writing code because you don’t have to worry about the concurrency issues, this also means you can’t perform long operations such as network access or image processing without blocking the main thread.

Imagine requesting some data from an API. Depending upon the situation the server might take some time to process the request while blocking the main thread making the web page unresponsive.

That’s where asynchronous JavaScript comes into play. Using asynchronous JavaScript (such as callbacks, promises, and async/await), you can perform long network requests without blocking the main thread.
:::

[Execution Context and Call Stack](../JSBase/executionContext.md)
JavaScript has a single call stack because it’s a single-threaded programming language. The call stack has a LIFO structure which means that the items can be added or removed from the top of the stack only.

## How Does Asynchronous JavaScript Work?
We use asynchronous callbacks to make our code non-blocking.

![An Overview of JavaScript Runtime Environment](../images/eventLoop.jpg)
The event loop, **the web APIs** and the message queue/task queue are not part of the JavaScript engine, it’s a part of browser’s JavaScript runtime environment or Nodejs JavaScript runtime environment (in case of Nodejs). In Nodejs, the web APIs are replaced by the C/C++ APIs.
```js
const networkRequest = () => {
  setTimeout(() => {
    console.log('Async Code');
  }, 2000);
};
console.log('Hello World');
networkRequest();
```
When the above code loads in the browser, the console.log(‘Hello World’) is pushed to the stack and popped off the stack after it’s finished. Next, a call to networkRequest() is encountered, so it’s pushed to the top of the stack.

Next setTimeout() function is called, so it’s pushed to the top of the stack. The setTimeout() has two arguments: 1) callback and 2) time in milliseconds (ms).

The setTimeout() method starts a timer of 2s in the web APIs environment. At this point, the setTimeout() has finished and it’s popped off from the stack. After it, console.log('The End') is pushed to the stack, executed and removed from the stack after its completion.

Meanwhile, the timer has expired, now the callback is pushed to the message queue. But the callback is not immediately executed, and that’s where the event loop kicks in.

### event loop
The job of the Event loop is to look into the call stack and determine if the call stack is empty or not. If the call stack is empty, it looks into the message queue to see if there’s any pending callback waiting to be executed.

1. promise catch try finally 
3. promise.polify
3. 关于promise.then的问题
```js
if(!Promise.allSettled) {
  const rejectHander = (reason) => {
    return {status: 'reject', reason}
  }

  const resolveHander = (value) => {
    return {status: 'resolve', value}
  }

  Promise.allSettled = function(promises) {
    const convertedPromises = promises.map(p => Promise.resolve(p).then(resolveHandler, rejectHandler));

    return Promise.all(convertedPromises);
  }
}
```

#### DOM Events
The Message queue also contains the callbacks from the DOM events such as click events and keyboard events. 
```js
document.querySelector('.btn').addEventListener('click',(event) => {
  console.log('Button Clicked');
});
```
In case of DOM events, the event listener sits in the web APIs environment waiting for a certain event (click event in this case) to happen, and when that event happens, then the callback function is placed in the message queue waiting to be executed.
### ES6 Job Queue/ Micro-Task queue
ES6 introduced the concept of job queue/micro-task queue which is used by Promises in JavaScript. The difference between the message queue and the job queue is that the job queue has a higher priority than the message queue, which means that promise jobs inside the job queue/ micro-task queue will be executed before the callbacks inside the message queue.
```js
console.log('Script start');
setTimeout(() => {
  console.log('setTimeout');
}, 0);
new Promise((resolve, reject) => {
    resolve('Promise 1 resolved');
  }).then(res => console.log(res));
new Promise((resolve, reject) => {
  resolve('Promise 2 resolved');
  }).then(res => {
       console.log(res);
       return new Promise((resolve, reject) => {
         resolve('Promise 3 resolved');
       })
     }).then(res => console.log(res));
console.log('Script End');

// Script start
// Script End
// Promise 1 resolved
// Promise 2 resolved
// Promise 3 resolved
// setTimeout
```

### Micro-task vs Macro-task
- 常见的宏任务有：script（整体代码）/setTimout/setInterval/setImmediate(node 独有)/requestAnimationFrame(浏览器独有)/IO/UI render（浏览器独有）/messageChannel
- 常见的微任务有：process.nextTick(node 独有)/Promise.[then/cath/finally]/Async/Await(就是Promise)/Object.observe/MutationObserver(浏览器环境，html5新特性)/queueMicrotask
![An Overview of JavaScript Runtime Environment](../images/eventLoop.png)
::: details 如何理解 script（整体代码块）是个宏任务呢?
实际上如果同时存在两个 script 代码块，会首先在执行第一个 script 代码块中的同步代码，如果这个过程中创建了微任务并进入了微任务队列，第一个 script 同步代码执行完之后，会首先去清空微任务队列，再去开启第二个 script 代码块的执行。所以这里应该就可以理解 script（整体代码块）为什么会是宏任务。

1. 同步代码不是宏任务，script整体代码在web中运行，web环境将其视为宏任务。
2. 同步代码直接进入执行栈，执行栈清空，检查微任务队列，若不为空，则将队首微任务推入执行栈，直至微任务队列清空，再检查宏任务队列。每次执行栈清空都会优先检查微任务队列。
```js
setTimeout(()=>{
  console.log(1)
  Promise.resolve().then(()=>{
    console.log(3)
  })
})

setTimeout(()=>{
  console.log(2)
})
// 1,3,2
```
:::

**macro-task queue vs task queue**
the macro-task queue works the same as the task queue. The only small difference between the two is that the task queue is used for synchronous statements whereas the macro-task queue is used for asynchronous statements.
It is worth mentioning that the execution of a JavaScript code execution is itself a macro-task. 

**定时器不准时**
setTimeout的回调不一定在指定时间后能执行。而是在指定时间后，将回调函数放入事件循环的队列中。
如果时间到了，JS引擎还在执行同步任务，这个回调函数需要等待；如果当前事件循环的队列里还有其他回调，需要等其他回调执行完。
另外，setTimeout 0ms 也不是立刻执行，它有一个默认最小时间，为4ms。
```js
// 因为取出第一个宏任务之前在执行全局Script，如果这个时间大于 4ms，这时 setTimeout 的回调函数已经放入队列，就先执行 setTimeout；如果准备时间小于 4ms，就会先执行 setImmediate。

// node
setTimeout(() => {
  console.log('setTimeout')
}, 0)
setImmediate(() => {
  console.log('setImmediate')
})
```
```js
async function async1() {
  // 使用await关键字与Promise.then效果类似  await Promise.resolve()
  // 同步1
  await async2() 
  // 相当于Promise.then()里的回调 
  // 微任务1
  console.log('async1 end')
}
async function async2() {
  console.log('async2 end') // 同步2
}
setTimeout(()=>{
  console.log('setTimeout') // 宏任务1
},0)
async1()
new Promise((resolve,reject)=> {
  console.log('promise') // 同步3
  resolve()
}).then(()=>{
  console.log('promise1') // 微任务1
}).then(()=>{
  console.log('promise2') // 微任务2
})
// async2 end
// promise
// async1 end
// promise1
// promise2
// setTimeout
```

```js
<ul id="myLink">
  <li id="1">aaa</li>
  <li id="2">bbb</li>
  <li id="3">ccc</li>
</ul>

let ul = document.querySelector('#myLink')
let liArray =document.querySelectorAll('i')
for(let i=0; i<liArray.length; i++) {
  liArray[i].onclick = function(e){
    target = e.target || e.src
  }
}

transition-property infinity
transition-during
transition-delay
transition-timing-function liner ease-out ease-in ease-in-out
cubic-bezier
```

### 异步结果获取
1. 回调（推荐）
2. 轮询（不推荐）
```js
// 1. 回调
function buyFruit(fn) {
  setTimeout(()=>{
    fn.call(undefined, '已买好水果')
  }, (Math.random()*10+5)*1000)
}
buyFruit(function(){
  console.log(arguments[0])
})
```
```js
// 2. 轮询
function buyFruit() {
  setTimeout(()=>{
    window.fruit = '已买好水果'
  }, (Math.random()*10+5)*1000)
}
buyFruit()
let fruitId = setInterval(()=>{
  if(window.fruit){
    console.log(window.fruit)
    window.clearInterval(fruitId)
  } else {
    console.log('没有东西')
  }
}, 1000)
```
```js
// 回调 Promise
function buyFruit() {
  var fn = (fnSuccess, fnError)=>{
    setTimeout(()=>{
      fnSuccess('已买好水果')
    },1000)
  }
  return new Promise(fn) // fn.call(undefined, success, error)
}
// 进一步简化
function buyFruit() {
  return new Promise((resolve, reject)=>{
    setTimeout(()=>{
      resolve('已买好水果')
    },1000)
  }) 
}
// 使用
var promise = buyFruit()
promise.then((res)=>{
  console.log(res)
}).catch(()=>{})
```
```js
// async/await
var result = await buyFruit()
```
#### 回调的形式
1. Node.js 的 error-first 形式
```js
fs.readFile('./1.txt', (error, content)=>{
  if(error){
    // 失败
  } else {
    // 成功
  }
})

function buyFruit(fn) {
  setTimeout(()=>{
    if(Math.random > 0.5) {
      fn.call(undefined, '买到的水果')
    } else {
      fn.call(undefined, new Error())
    }
  }, (Math.random()*10+5)*1000)
}
buyFruit(function(r){
  if(r instanceof Error) {
    console.log('没买')
  }else {
    console.log('买了')
  }
})
```
2. jQuery 的 success/error
```js
$.ajax({
  url:'',
  success: ()=>{},
  error: ()=>{}
})
```
3. jQuery 的 donw/fail/always
```js
$.ajax({
  url:''
}).done(()=>{}).fail(()=>{}).always(()=>{})
```
4. Promise 的 then
```js
// 前端提出Promise规范，所有的异步操作都回复一个带有属性then的对象， then必须可以接收两个函数作为参数（一个成功，一个失败），并且返回的仍未带有then的对象
axios.ajax({
  url:''
}).then((response)=>{ 
  let x = !!response
  return x
}, (error)=>{
  console.log('fail')
}).then((x)=>{
  console.log('fail') // 即使失败也会执行到这里
})
```

### 前端经常遇见的异步
```js
document.getElementsByTagNames('img')[0].width // 宽度为0

document.getElementsByTagNames('img')[0].onload = function() {
  console.log(this.width) // 宽度不为0
}
```
```js
let liList = document.querySelectorAll('li')
for(var i=0; i<liList.length; i++) { // 同步
  liList[i].onclick = function(){ // 异步
    console.log('i')
  }
}

let liList = document.querySelectorAll('li')
for(let i=0; i<liList.length; i++) { 
  liList[i].onclick = function(){ // i的作用域在当前花括号内，相当于 j=i, console.log(j)
    console.log(i)
  }
}
```
```js
// 后端转前端 经常会把AJAX转出同步的
let request = $.ajax({
  url: '.',
  async: false 
})
console.log(request.responseText)

let request = $.ajax({
  url: '.',
  async: true,
  success: function(response) { // 异步：请求成功后，浏览器调用该回调
    console.log(response)
  } 
})
console.log(request.responseText)
```

## Promise
In JavaScript, promises and async/əˈsɪŋk//await are two different ways to handle asynchronous/eɪˈsɪŋkrənəs/ operations. 

Promise
A promise is an object that eventually leads to an asynchronous operation’s completion or failure. A promise can be in one of three states: pending, fulfilled, or rejected. When the asynchronous operation is completed, the Promise will either be fulfilled with a value or rejected with an error.

1.[Understanding Asynchronous JavaScript](https://blog.bitsrc.io/understanding-asynchronous-javascript-the-event-loop-74cd408419ff)
2.[The JavaScript Event Loop: Explained](https://towardsdev.com/event-loop-in-javascript-672c07618dc9)
3.[Understanding Promises in JavaScript](https://blog.bitsrc.io/understanding-promises-in-javascript-c5248de9ff8f)