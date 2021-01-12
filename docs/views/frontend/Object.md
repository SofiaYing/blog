### 知识补充:for in/ Object.keys/ Object.getOwnPropertyName
```js
var parent = Object.create(Object.prototype, {
    a: {
        value: 1,
        writable: true,
        enumerable: true, //可枚举
        configurable: true            
    }
});
//child继承parent
var child = Object.create(parent, {
    b: {
        value: 2,
        writable: true,
        enumerable: true,
        configurable: true
    },
    c: {
        value: 3,
        writable: true,
        enumerable: false,
        configurable: true
    }
});
```
- for in:es3,输出自身以及原型链上可枚举的属性
```js
for(var key in child){
  console.log(key)
}
// b a
```
注意：不同的浏览器对for in属性输出的顺序可能不同。
如果仅想输出自身的属性可以借助 hasOwnProperty。可以过滤掉原型链上的属性。
```js
for(var key in child){
  if(child.hasOwnProperty(key)){
    console.log(key)
  }
}
//b
```
- Object.keys: es5,用来获取对象自身可枚举的属性键
```js
console.log(Object.keys(child))
// ["b"]
```
- Object.getOwnPropertyNames:es5,用来获取对象自身的全部属性名。
```js
console.log(Object.getOwnPropertyName(child))
//["b","c"]
```
在es3中，我们不能定义属性的枚举性，所以也不需要那么多方法，有了keys和getOwnPropertyNames后基本就用不到for in了