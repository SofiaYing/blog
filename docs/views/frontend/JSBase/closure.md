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
for(var i = 0; i < 6; i++){
  setTimeout(() => {
    console.log(i)
  },100)
}
console.log(i)
```
(闭包详解一)[https://juejin.cn/post/6844903612879994887]
(闭包详解二：JavaScript中的高阶函数)[https://juejin.cn/post/6844903616885555214]