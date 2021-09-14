### 栗子1
1. 仍然保留现在所有的功能
2. 增加发射导弹功能
```js
function Plane() {}
Plane.prototype.fire = function () {
  console.log('fire')
}

function upgrade(target, callback) {
  // 1. 缓存老方法
  const oldFn = Plane.prototype[target]
  // 2. 重新改写老方法
  Plane.prototype[target] = function (...args) {
    // 3. 添加新的功能
    callback()
    // 4. 调用老功能
    oldFn.apply(this, args)
  }
}

for (let name in Plane.prototype) {
  upgrade(name, function () {
    // ...
  })
}

upgrade('fire', () => {
  console.info('发射导弹，咚咚咚')
})

const p = new Plane()

p.fire()

upgrade('fire', () => {
  console.info('投掷炸弹，DuangDuangDuang')
})

p.fire()
```
### 栗子2
继承
```js
function Plane() {}

Plane.prototype.fire = function () {
  console.info('开枪，哒哒哒')
}

class NewPlane extends Plane {
  fire() {
    console.info('发射导弹2')
    // 执行 super.method(...) 来调用一个父类方法。
    // 执行 super(...) 来调用一个父类 constructor（只能在我们的 constructor 中用）
    super.fire()
  }
}

const np = new NewPlane()
np.fire()
```
### 栗子3
ES7 装饰器
```js
// target 就是被装饰的类本身
@log 
class Plane {
  @upgrade
  fire() {
    console.info('开枪，哒哒哒')
    return true
  }
}

function log(target) {
  target.prototype.name = 'zac'
}

const p = new Plane()

console.info(p.name)

/**
 * 添加导弹
 * @param target
 * @param name
 * @param descriptor
 * Object.defineProperty(options)
 */
function upgrade(target, name, descriptor) {
  const oldFn = descriptor.value
  descriptor.value = function (...args) {
    console.info('发射导弹')
    const result = oldFn.apply(this, args)
    console.info('投掷砸蛋')
    return result
  }
}

const p = new Plane()
p.fire()
```
### ES6 装饰器
toString valueOf
Function.toString() 可以通过console打印出这个函数
descriptor
Object.definePrototy(options)
### 实栗1
批量为许多函数方法增加性能检测，比如运行时间。、
### 实栗2
vuex mixin.js
```js
export default function (Vue) {
  const version = Number(Vue.version.split('.')[0])

  if (version >= 2) {
    Vue.mixin({ beforeCreate: vuexInit })
  } else {
    // override init and inject vuex init procedure
    // for 1.x backwards compatibility.
    // 1.缓存老方法
    const _init = Vue.prototype._init
    // 2.重写老方法
    Vue.prototype._init = function (options = {}) {
      // 3. 将新方法作为参数传给老方法
      options.init = options.init
        ? [vuexInit].concat(options.init)
        : vuexInit
      // 调用
      _init.call(this, options)
    }
  }

  /**
   * Vuex init hook, injected into each instances init hooks list.
   */

  function vuexInit () {
    const options = this.$options
    // store injection
    if (options.store) {
      this.$store = typeof options.store === 'function'
        ? options.store()
        : options.store
    } else if (options.parent && options.parent.$store) {
      this.$store = options.parent.$store
    }
  }
}
```
### 实栗3
vue array.js
```js
import { def } from '../util/index'

const arrayProto = Array.prototype
export const arrayMethods = Object.create(arrayProto)

const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]

/**
 * Intercept mutating methods and emit events
 */
methodsToPatch.forEach(function (method) {
  // cache original method
  // 缓存老方法
  const original = arrayProto[method]
  def(arrayMethods, method, function mutator (...args) {
    // 调用老方法
    const result = original.apply(this, args)
    const ob = this.__ob__
    let inserted
    // 新增的数据需要添加监听
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }
    if (inserted) ob.observeArray(inserted)
    // notify change
    ob.dep.notify()
    return result
  })
})
```
