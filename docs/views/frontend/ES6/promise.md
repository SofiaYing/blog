---
title: Promise
date: 2023-9-6
categories:
  - frond-end
tags :
  - asynchronous
  - promise
---
## Callback Hell
JavaScript is a single-threaded programming language, that means only one thing can happen at a time. Before ES6, we used callbacks to handle asynchronous tasks such as network requests.

Using promises, we can avoid the infamous ‘callback hell’ and make our code cleaner, easier to read, and easier to understand.

This is what we call callback hell, where each callback is nested inside another callback, and each inner callback is dependent on its parent.
```js
// Suppose we want to get some data from a server asynchronously
getData(function(x){ //request some data from the server by calling the getData() function, which receives the data inside the callback function.
    console.log(x);
    getMoreData(x, function(y){
        console.log(y); 
        getSomeMoreData(y, function(z){ 
            console.log(z);
        });
    });
});
```

Promise
```js
getData()
  .then((x) => {
    console.log(x);
    return getMoreData(x);
  })
  .then((y) => {
    console.log(y);
    return getSomeMoreData(y);
  })
  .then((z) => {
    console.log(z);
   });
```
## Promise
A Promise is an object that holds the future value of an async operation. 

### States of a Promise
一旦状态改变，就不会再变
1. unresolved (pending): A Promise is pending if the result is not ready. That is, it’s waiting for something to finish (for example, an async operation).
2. resolved (fulfilled): A Promise is resolved if the result is available. That is, something finished (for example, an async operation) and all went well.
3. rejected: A Promise is rejected if an error happened.

`A promise can be resolved or rejected only once. Further invocation of resolve() or reject() has no effect on Promise state.`

### Creating a Promise
```js
const promise = new Promise((resolve, reject) => {
  // Promise 新建后就会立即执行。
  // The callback(executor function) is immediately executed when a promise is created. The promise is resolved by calling the resolve() and rejected by calling reject().
  console.log('promise')
  if('success'){
    resolve('1')
    console.log('2') //调用resolve(1)以后，后面的console.log(2)还是会执行，并且会首先打印出来。这是因为立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。
  } else {
    reject()
  }
}).then((value)=>{ // then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行
  console.log('resolved')
})
console.log('end')

// promise
// end
// resolved
```
### Consuming a Promise

We use promise chaining when we want to resolve promises in a sequence.
#### Promise.prototype.then()
`.then() syntax: promise.then(successCallback, failureCallback)`
- if the promise is resolved, the successCallback is called with the value passed to resolve()(if the promise is resolved, the successCallback is called with the value passed to resolve())
- The failureCallback is called when a promise is rejected. It takes one argument which is the value passed to reject().

then方法是定义在原型对象Promise.prototype上的。它的作用是为 Promise 实例添加状态改变时的回调函数。前面说过，then方法的第一个参数是resolved状态的回调函数，第二个参数是rejected状态的回调函数，它们都是可选的。

then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因此可以采用链式写法，即then方法后面再调用另一个then方法。

The then() and catch() methods can also return a new promise which can be handled by chaining another then() at the end of the previous then() method.

We use promise chaining when we want to resolve promises in a sequence.
```js
getJSON("/post/1.json").then(
  post => getJSON(post.commentURL)
).then(
  comments => console.log("resolved: ", comments),
  err => console.log("rejected: ", err)
);
```
#### Promise.prototype.catch()
`.catch() syntax: promise.catch(failureCallback)`
We use catch() for handling errors. It’s more readable than handling errors inside the failureCallback callback of the then() callback. 
catch()是.then(null, rejection)或.then(undefined, rejection)的别名，用于指定发生错误时的回调函数。

一般来说，不要在then()方法里面定义 Reject 状态的回调函数（即then的第二个参数），总是使用catch方法。

Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个catch语句捕获。
```js
getJSON('/post/1.json').then(function(post) {
  return getJSON(post.commentURL);
}).then(function(comments) {
  // some code
}).catch(function(error) {
  // 处理前面三个Promise产生的错误
});
```
Promise 内部的错误不会影响到 Promise 外部的代码
```js
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为x没有声明
    resolve(x + 2);
  });
};

someAsyncThing().then(function() {
  console.log('everything is great');
});

setTimeout(() => { console.log(123) }, 2000);
// Uncaught (in promise) ReferenceError: x is not defined
// 123
```

#### Promise.all()
Promise.all()方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

The then() and catch() methods can also return a new promise which can be handled by chaining another then() at the end of the previous then() method.

We use promise chaining when we want to resolve promises in a sequence.

This method can be useful when you have more than one promise, and you want to know when all of the promises have resolved. For example, if you are requesting data from different APIs and you want to do something with the data only when all of the requests are successful.

So Promise.all() waits for all promises to succeed and fails if any of the promises in the array fails.
```js
const p = Promise.all([p1, p2, p3]);
```
p的状态由p1、p2、p3决定，分成两种情况。
1. 只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
2. 只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。
3. 如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch方法。
```js
// p1会resolved，p2首先会rejected，但是p2有自己的catch方法，该方法返回的是一个新的 Promise 实例，p2指向的实际上是这个实例。该实例执行完catch方法后，也会变成resolved，导致Promise.all()方法参数里面的两个实例都会resolved，因此会调用then方法指定的回调函数，而不会调用catch方法指定的回调函数。

如果p2没有自己的catch方法，就会调用Promise.all()的catch方法。
const p1 = new Promise((resolve, reject) => {
  resolve('hello');
})
.then(result => result)
.catch(e => e);

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了');
})
.then(result => result) // return result
.catch(e => e);

Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e));
// ["hello", Error: 报错了]
```
#### Promise.race()
Promise.race()方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

This method takes an array of promises as input and returns a new promise that fulfills as soon as one of the promises in the input array fulfills or rejects as soon as one of the promises in the input array rejects. 
```js
const p = Promise.race([p1, p2, p3]);
```
只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。
#### Promise.allSettled()
ES2020引入，接受一个数组作为参数，数组的每个成员都是一个 Promise 对象，并返回一个新的 Promise 对象。只有等到参数数组的所有 Promise 对象都发生状态变更（不管是fulfilled还是rejected），返回的 Promise 对象才会发生状态变更。
```js
const promises = [ fetch('index.html'), fetch('https://does-not-exist/') ];
const results = await Promise.allSettled(promises);

// 过滤出成功的请求
const successfulPromises = results.filter(p => p.status === 'fulfilled');

// 过滤出失败的请求，并输出原因
const errors = results
  .filter(p => p.status === 'rejected')
  .map(p => p.reason);
```
#### Promise.any()
ES2021 引入了Promise.any()方法。该方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例返回。

只要参数实例有一个变成fulfilled状态，包装实例就会变成fulfilled状态；如果所有参数实例都变成rejected状态，包装实例就会变成rejected状态。

Promise.any()跟Promise.race()方法很像，只有一点不同，就是Promise.any()不会因为某个 Promise 变成rejected状态而结束，必须等到所有参数 Promise 变成rejected状态才会结束。

#### Promise.resolve() 
将现有对象转为 Promise 对象
1. 参数为Promise实例,那么Promise.resolve将不做任何修改、原封不动地返回这个实例。
2. 参数是一个thenable对象,thenable对象指的是具有then方法的对象，会将这个对象转为 Promise 对象，然后就立即执行thenable对象的then()方法。
3. 参数不是具有then()方法的对象，或根本就不是对象，返回一个新的 Promise 对象，状态为resolved。
4. 不带参数，则直接返回一个resolved状态的 Promise 对象。如果希望得到一个 Promise 对象，比较方便的方法就是直接调用Promise.resolve()方法。`const p = Promise.resolve();`

```js
// 立即resolve()的 Promise 对象，是在本轮“事件循环”（event loop）的结束时执行，而不是在下一轮“事件循环”的开始时。
setTimeout(function () { 
  console.log('three');
}, 0);

Promise.resolve().then(function () { 
  console.log('two');
});

console.log('one');

// one
// two
// three
```
#### Promise.reject()
会返回一个新的 Promise 实例，该实例的状态为rejected
```js
axios({
  url:'',
  async: true
}).then(()=>{
  Promise.reject('error') //可以用来主动失败
}).catch(()=>{})
```

#### Promise.prototype.finally()
finally()方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。
```js
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```
### Promise实践
```js
getLocation() {
  if(!this.map) return Promise.reject('map is null');
  return new Promise((resolve,reject)=>{
    this.map.getLocation({
      success: resolve, // (data) => resolve(data)
      fail: reject
    })
  })
}

getLocation().then((data)=>{
  this.triggerEvent('change', {
    data, 
    map: this.map,
    callback: endCallback
  })
})
```
#### 异步图片加载
```js
function loadImageAsync(url) {
  return new Promise(function(resolve, reject) {
    const image = new Image();

    // image.onload = resolve
    image.onload = function() {
      resolve(image);
    };
    // image.onerror = reject
    image.onerror = function() {
      reject(new Error('Could not load image at ' + url));
    };

    image.src = url;
  });
}
```
#### 封装请求
```js
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();
  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```
### 如何实现一个简单的Promise
```js
//注意输入是什么，输出是什么
function Promise(fn) {
  let status = 'pending'

  function successNotify(){ // resolve
    status = 'resolved'
    toDoThen()
  }
  function failNotify(){ // reject
    status = 'rejected'
    toDoThen()
  }

  function toDoThen() {
    if (status === 'resolved') {
      for(let i=0; i<successArray.length; i++) {
        successArray[i].call() 
      }
    } else if () {
      for(let i=0; i<failArray.length; i++) {
        successArray[i].call() 
      }
    }
  }

  let successArray = []
  let failArray = []

  fn.call(undefined, successNotify, failNotify)

  return {
    then: function(successFn, failFn){
      successArray.push(successFn)
      failArray.push(failFn)
      return undefined // 正常应该返回一个含有then属性的对象，用到递归
    }
  }
}

var p = new Promies((resolve,reject)=>{
  setTimeout(()=>{ 
    resolve('success') // 异步操作，执行结束就会调用相应函数
  },1000)
}).then((res)=>{ // 马上就调用then，将要做的事情传给了Promise,Promise将其添加至successArray
  console.log(res)
})
```
## References
1.[Understanding Promises in JavaScript](https://blog.bitsrc.io/understanding-promises-in-javascript-c5248de9ff8f)
2.[阮一峰 Promise](https://es6.ruanyifeng.com/#docs/promise)