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

// 闭包方式
Person.getInstance = (function (){
  let instance = null
  return function() {
    if (!instance) {
      instance = new Person()
    }
    return instance
  }
})()
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

### 实栗1
Vue.use
```js
function initUse (Vue) {
  Vue.use = function (plugin) {
    // 此处为单例
    // this:vue 
    // 1. this._installedPlugins 缓存所有插件
    // 2. 如果已存在，则直接返回
    var installedPlugins = (this._installedPlugins || (this._installedPlugins = []));
    if (installedPlugins.indexOf(plugin) > -1) {
      return this
    }

    // additional parameters
    // 将Vue.use传进来的自定义参数转换成数组
    var args = toArray(arguments, 1);
    // 将Vue当做第一个参数，比如install(Vue)这里能直接能拿到Vue
    args.unshift(this);
    // 如果是对象的话，里面会有一个install函数
    if (typeof plugin.install === 'function') {
      // 直接执行并将这个对象作为函数的this,并将参数传过去
      plugin.install.apply(plugin, args);
    } else if (typeof plugin === 'function') {
      // 直接执行并将null作为函数的this,并将参数传过去
      plugin.apply(null, args);
    }
    installedPlugins.push(plugin);
    return this
  };
}
```

### 实栗2
vuex store.js
Vuex 插件是一个对象，它在内部实现了一个 install 方法，这个方法会在插件安装时被调用，从而把 Store 注入到Vue实例里去。也就是说每 install 一次，都会尝试给 Vue 实例注入一个 Store。
即一个 Vue 实例只能对应一个 Store。
```js
let Vue // 这个Vue的作用和之前的栗子中的instance作用一样
...
export function install (_Vue) {
  // 判断传入的Vue实例对象是否已经被install过Vuex插件（是否有了唯一的state）
  if (Vue && _Vue === Vue) {
    if (__DEV__) {
      console.error(
        '[vuex] already installed. Vue.use(Vuex) should be called only once.'
      )
    }
    return
  }
  // 若没有，则为这个Vue实例对象install一个唯一的Vuex
  Vue = _Vue
  // 将Vuex的初始化逻辑写进Vue的钩子函数里
  applyMixin(Vue)
}
```
假如 install 里没有单例模式的逻辑，如果在一个应用里不小心多次安装了插件，失去了单例判断能力的 install 方法，会为当前的Vue实例重新注入一个新的 Store，也就是说你中间的那些数据操作全都没了，一切归 0。因此，单例模式在此处是非常必要的。

[JavaScript 设计模式核⼼原理与应⽤实践](https://juejin.cn/book/6844733790204461070/section/6844733790267375624)