## 知识补充:for in/ Object.keys/ Object.getOwnPropertyName
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
### for in:es3,输出自身以及原型链上可枚举的属性
```js
for(var key in child){
  console.log(key)
}
// b a
```
注意：不同的浏览器对for in属性输出的顺序可能不同。
如果仅想输出自身的属性可以借助 **hasOwnProperty**。可以过滤掉原型链上的属性。
```js
for(var key in child){
  if(child.hasOwnProperty(key)){
    console.log(key)
  }
}
//b
```
`注意`
for in 用来循环数组不是一个合适的选择。
- 迭代的是属性key，不是值
- 由于属性 key 是字符串，迭代出的元素索引是 string,不是 number.
- 迭代的是数组实例上所有可枚举的属性key,而不是数组内元素。
如果想获取一个对象所有的可枚举属性(包含原型链上的)，那么 for in 倒是可以胜任，若仅仅是对象自身声明的属性，那 Object.keys 更合适。

### Object.keys: es5,用来获取对象自身可枚举的属性键
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