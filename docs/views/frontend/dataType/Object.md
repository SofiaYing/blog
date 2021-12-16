
对象是具有一些特殊特性的关联数组。
它们存储属性（键值对），其中：
- 属性的键必须是字符串或者 symbol（通常是字符串）。
- 值可以是任何类型。

JavaScript 中有很多类型的对象 如：
- Array 用于存储有序数据集合，
- Date 用于存储时间日期，
- Error 用于存储错误信息。

常见操作
- 删除属性：delete obj.prop
- 检查是否存在给定键的属性：
  - "key" in obj。
  - obj[key] === 'undefined' 
- 遍历对象：for(let key in obj) 循环。

`Object.assign()`
`hasOwnProperty` 方法会返回一个布尔值，指示对象自身属性中是否具有指定的属性（也就是，是否有指定的键）。

### 浅拷贝一个对象
**方法一 循环遍历**
```js
let obj = {
  name: 'zhang',
  age: 30
}

let clone = {}

for (let key in obj) {
  clone[key] = obj[key]
}

clone.name = 'han'
console.log(clone.name, obj.name) // 'han' 'zhang'
```
**方法二 Object.assign** 用于将所有可枚举属性的值从一个或多个源对象分配到目标对象。它将返回目标对象。
```js
/**
 * @description 方法用于将所有可枚举属性的值从一个或多个源对象分配到目标对象。它将返回目标对象。
 * @param dest 是指目标对象
 * @param sources（可按需传递多个参数）是源对象。
 * @return dest
 */
Object.assign(target, ...sources)

let user1 = { name: "John" };
let user2 = { name: "Pete" };
let user3 = { name: "Bob", age: 14}
Object.assign(user1, user2, user3);
// 如果被拷贝的属性的属性名已经存在，那么它会被覆盖
console.log(user1); // { name: "Bob", age:14 }

// 浅拷贝
let clone = Object.assign((), user1)
```
**方法三 spread语法**
```js
let clone = {...user}
```
### 深拷贝（详见clone.md）



## 垃圾回收
对于开发者来说，JavaScript 的内存管理是自动的、无形的。我们创建的原始值、对象、函数……这一切都会占用内存。
当我们不再需要某个东西时会发生什么？JavaScript 引擎如何发现它并清理它？
### 可达性（Reachability）
**JavaScript 中主要的内存管理概念是 可达性。**
在 JavaScript 引擎中有一个被称作 垃圾回收器 的东西在后台执行。它监控着所有对象的状态，并删除掉那些已经不可达的。

- 垃圾回收是自动完成的，我们不能强制执行或是阻止执行。
- 当对象是可达状态时，它一定是存在于内存中的。
- 被引用与可访问（从一个根）不同：一组相互连接的对象可能整体都不可达。
如下图
![垃圾回收](./images/垃圾回收.png)
对外引用不重要，只有传入引用才可以使对象可达。所以，John 现在是不可达的，并且将被从内存中删除，同时 John 的所有数据也将变得不可达。

(你真的了解垃圾回收机制吗)[https://juejin.cn/post/6981588276356317214]
(JavaScript中的垃圾回收和内存泄漏)[https://juejin.cn/post/6844903833387155464]