保证一个类仅有一个实例，并提供一个访问它的全局访问点，这样的模式就叫做单例模式。

### 栗子一
```js
const single = {}
```

### 栗子二
```js
// module -> import/export
export const utils = {
  trim() {},
  handle() {}
}
```

### 栗子三
```js
//怎么new 都返回一个实例
function Person(name) {
  if (Person.instance) {
    return Person.instance
  }
  this.name = name

  Person.instance = this
}
```

### 栗子四
期望：
1. 提供一个方法，每次我调用这个方法的时候，返回同一个实例
2. new 仍然创建新的对象
```js
function Person(name) {
  this.name = name
}

Person.getInstance = function (...args) {
  if (!Person.instance) {
    Person.instance = new Person(...args)
  }
  return Person.instance
}
```
懒函数写法
```js
function Person(name, age) {
  this.name = name
  this.age = age
}

Person.getInstance = function (...args) {
  const instance = new Person(...args)
// 懒函数
  Person.getInstance = function () {
    return instance
  }
  return Person.getInstance()
}
```
class写法
```js
class Person {
  constructor(name) {
    this.name = name
  }
  // Person.prototype
  getDetails() {
    console.info('getDetails')
  }
  // Person.getInstance
  static getInstance(...args) {
    const instance = new Person(...args)

    Person.getInstance = function () {
      return instance
    }
    return Person.getInstance()
  }
}
```
isPrototypeOf、Object.getPrototypeOf、hasOwnProperty、Object.getOwnPropertyName、Object.keys
- class的prototype: 不可枚举，可以通过Object.getOwnPropertyName进行枚举
- function的prototype： 可枚举
