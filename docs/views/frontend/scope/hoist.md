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