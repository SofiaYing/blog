---
title: vue数据绑定
date: 2020-11-25
categories:
  - frond-end
tags :
  - open source project
  - vue
---
## 概述
|Vue1.0|Vue2.0|
|-|-|
|一个对象属性对应一个dep，一个dep对应多个watcher(watcher也是细粒度的)(一个对象属性可能在多个标签使用，那么就会有对应多个watcher，这些watcher都会放入到这个对象属性唯一对应的dep中)，但数据过大时，就会有很多个watcher，就会出现性能问题；|引入VDOM，给每个vue组件绑定一个watcher(渲染watcher)，这个组件上的数据的dep中都包含有该watcher，当该组件数据发生变化时，就会通知watcher触发update方法，生成VDOM，和旧的VDOM进行比较，更新改变的部分，极大的减少了watcher的数量，优化了性能；（所以，在Vue2.0中是一个组件对应一个watcher）|
## 响应式原理
涉及到的知识点
1. MVVM
2. typeof

|Observer|Dep|Watcher|
|-|-|-|
|辅助的可观测类，数组/对象通过它的转化，可成为可观测数据|扮演观察目标的角色，每一个数据都会有Dep类实例，它内部有个subs队列，subs就是subscribers的意思，保存着依赖本数据的观察者，当本数据变更时，调用dep.notify()通知观察者|扮演观察者的角色，进行观察者函数的包装处理。如render()函数，会被进行包装成一个Watcher实例| 
### 定义VUE类
```js
class Vue {
  constructor(options) {
    this._data = options.data
    observer(this._data)
  }
}
```
### 监听
```js
// 对需要监听的数据对象进行遍历、给它的属性加上定制的 getter 和 setter 函数。
// 这样但凡这个对象的某个属性发生了改变，就会触发 setter 函数，进而通知到订阅者。
function observer(data) {
  if (!data || (typeof(data) !== 'object')) {
    return
  }
  Object.keys(data).forEach((key) => {
    defineReactive(data,key,data[key])
  })
}
```
```js
function defineReactive(obj,key,val) {
  // 属性值也可能是object类型，这种情况下需要调用observe进行递归遍历
  observer(val)
  //Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。
  Object.defineProperty(obj,key,{
    enumerable: true, //属性可枚举
    configurable: true, //属性可删除
    get: function reactiveGetter () {
      return val
    }  //当访问该属性时，会调用此函数
    set: function reactiveSetter (newVal) {
      if (newVal === val) {
        return
      }
      cb(newVal)
    }  //当属性值被修改时，会调用此函数
  })
}

function cb(val) {
    /* 渲染视图 */
}
```
### 使用
```js
let instance = new Vue({
  data: {
    test: 'test'
  }
})
instance._data.test = 'hi'
```

## 进一步扩展cb，依赖收集
```js
class Vue {
  constructor(options) {
    this._data = options.data
    observer(this._data)
    new Watcher()
  }
}
```
```js
class Watcher {
  constructor() {
    Dep.target = this
    //会调用get
  }
  update() {
    //更新视图
  }
}

Dep.target = null 
```
```js
//Dep类实例依附于每个数据而出来，用来管理依赖数据的Watcher类实例
class Dep {
  constructor() {
    this.subs = []
  }
  addSub(sub) {
    this.subs.push(sub)
  }
  notify() {
    this.subs.forEach((sub) => {
      sub.update()
    })
  }
}
```

```js
function defineReactive(obj,key,val) {
  // Object.defineProperty()里的get/set方法相对于var dep = new Dep()形成了闭包，从而很巧妙地保存了dep实例
  const dep = new Dep()

  Object.defineProperty(obj,key,{
    enumerable: true, 
    configurable: true,
    get: function reactiveGetter() {
      dep.addSub(Dep.target)
    },
    set: function reactiveSetter(newVal) {
      if(newVal === val) {
        return
      }
      dep.notify()
    }
  })
}
```
## 补充一些源码
```js
class Vue {
  constructor(options) {
    this._data = options.data
    observer(this._data)
    new Watcher()
  }
}
```
```js
class Watcher {
  constructor() {
    Dep.target = this
    //会调用get
  }
  update() {
    //更新视图
  }

  get() {
      // targetStack.push(target)
      // Dep.target = target
      pushTarget(this)

      let value
      const vm = this.vm
      try {
          value = this.getter.call(vm, vm)
      } catch (e) {
          if (this.user) {
              handleError(e, vm, `getter for watcher "${this.expression}"`)
          } else {
              throw e
          }
      } finally {
          // "touch" every property so they are all tracked as
          // dependencies for deep watching
          if (this.deep) {
              traverse(value)
          }
          popTarget()
          this.cleanupDeps()
      }
      return value
  }

  addDep (dep: Dep) {
    const id = dep.id
    if (!this.newDepIds.has(id)) {
      this.newDepIds.add(id)
      this.newDeps.push(dep)
      //防止重复收集依赖，如页面内有两个地方用到同一个数据，但是只收集一次
      if (!this.depIds.has(id)) {  
        dep.addSub(this)
      }
    }
  }

  //在cleanupDeps中主要清除上一轮中的依赖在新一轮中没有重新收集的，也就是数据刷新后某些数据不再被渲染出来了
  cleanupDeps() {
    let i = this.deps.length
    while (i--) {
        const dep = this.deps[i]

        //newDep中没有的,从数据对应的dep中删除该watcher
        //cleanupDeps, 剔除上一次存在但本次渲染不存在的依赖
        if (!this.newDepIds.has(dep.id)) {
            dep.removeSub(this)
        }
    }
    let tmp = this.depIds
    this.depIds = this.newDepIds //depIds缓存最新的newDepIds
    this.newDepIds = tmp
    this.newDepIds.clear() //清空newDepIds  clear方法也是将array.length = 0
    tmp = this.deps
    this.deps = this.newDeps //dep缓存最新的newDeps
    this.newDeps = tmp
    this.newDeps.length = 0 //清空newDeps 
  }
}
```
```js
//Dep类实例依附于每个数据而出来，用来管理依赖数据的Watcher类实例
class Dep {
  constructor() {
    this.subs = []
  }
  addSub(sub) {
    this.subs.push(sub)
  }
  notify() {
    this.subs.forEach((sub) => {
      sub.update()
    })
  }
  //依赖收集，当存在Dep.target的时候添加观察者对象
  depend() {
    if (Dep.target) {
      Dep.target.addDep(this)
    }
  }
}

// Dep.target = null 是为了下次render触发get的时候不会重复收集依赖
// 由于JavaScript是单线程模型，所以虽然有多个观察者函数，但是一个时刻内，就只会有一个观察者函数在执行，那么此刻正在执行的那个观察者函数，所对应的Watcher实例，便会被赋给Dep.target这一类变量，从而只要访问Dep.target就能知道当前的观察者是谁。
Dep.target = null 
const targetStack = []

// pushTarget/popTarget用处参考下文的computed
export function pushTarget(target: ? Watcher) {
    targetStack.push(target)
    Dep.target = target
}

export function popTarget() {
    targetStack.pop()
    Dep.target = targetStack[targetStack.length - 1]
}
```
```js
function defineReactive(obj,key,val) {
  // Object.defineProperty()里的get/set方法相对于var dep = new Dep()形成了闭包，从而很巧妙地保存了dep实例
  // 存储每个属性关联的watcher队列，当setter触发时依然能访问到
  // 一个data中的属性唯一拥有一个Dep实例dep
  // 一个dep实例可对应多个watcher
  const dep = new Dep()

  //如果属性为对象也创建相应observer
  let childOb = observe(val)

  Object.defineProperty(obj,key,{
    enumerable: true, 
    configurable: true,
    get: function reactiveGetter() {
      // 收集依赖不直接使用addSub是为了能让Watcher创建时自动将自己添加到dep.subs中，这样只有当数据被访问时才会进行依赖收集，可以避免一些不必要的依赖收集。
      if (Dep.target) {
        dep.depend() //将当前dep传到对应watcher中再执行watcher.addDep将watcher添加到当前dep.subs中
        if (childOb) {  //如果属性是对象则继续收集依赖
          childOb.dep.depend()
          ...
        }
      }
      return value
    },
    set: function reactiveSetter(newVal) {
      if(newVal === val) {
        return
      }
      dep.notify()
      //数据变化时，执行watcher.update，会再次调用getter，
      //又重新将Dep.target指向当前执行的Watcher触发该Watcher的更新。
    }
  })
}
```

### computed
```js
<div id="app">
      <div>{{a}}</div>
      <div>{{b}}</div>
    </div>
new Vue({
  el: "#app",
  data() {
    return {
      a:1,
    }
  },
  computed:{
    b() {
      return a+1
    }
  },
})
```

```js
//instace/state.js
function initComputed (vm: Component, computed: Object) {
  // $flow-disable-line
  const watchers = vm._computedWatchers = Object.create(null)
  // (Object.create(null))[https://juejin.cn/post/6844903589815517192]
  // computed properties are just getters during SSR
  const isSSR = isServerRendering()

  for (const key in computed) {
    const userDef = computed[key]  //如示例中的b
    const getter = typeof userDef === 'function' ? userDef : userDef.get
    if (process.env.NODE_ENV !== 'production' && getter == null) {
      warn(
        `Getter is missing for computed property "${key}".`,
        vm
      )
    }
    // SSR...
    // component-defined computed properties are already defined on the
    // component prototype. We only need to define computed properties defined
    // at instantiation here.
    if (!(key in vm)) {
      defineComputed(vm, key, userDef)
    } 
}

function createComputedGetter (key) {
  return function computedGetter () {
    const watcher = this._computedWatchers && this._computedWatchers[key]
    if (watcher) {
      //watcher.dirty 参数决定了计算属性值是否需要重新计算，默认值为true，即第一次时会调用一次
      if (watcher.dirty) {
        // 每次执行之后watcher.dirty会设置为false，只要依赖的data值改变时才会触发
        // watcher.dirty为true,从而获取值时从新计算
        watcher.evaluate()
      }
      if (Dep.target) {
        watcher.depend()
      }
      return watcher.value
    }
  }
}
```
```js 
//wacher.js
/**
 * Evaluate the getter, and re-collect dependencies.
 */
get() {
    // targetStack.push(target)
    // Dep.target = target
    pushTarget(this)

    let value
    const vm = this.vm
    try {
        value = this.getter.call(vm, vm)
    } catch (e) {
        if (this.user) {
            handleError(e, vm, `getter for watcher "${this.expression}"`)
        } else {
            throw e
        }
    } finally {
        // "touch" every property so they are all tracked as
        // dependencies for deep watching
        if (this.deep) {
            traverse(value)
        }
        popTarget()
        this.cleanupDeps()
    }
    return value
}
/**
 * Evaluate the value of the watcher.
 * This only gets called for lazy watchers.
 */
evaluate() {
    this.value = this.get()
    this.dirty = false
}
/**
 * Depend on all deps collected by this watcher.
 */
depend() {
    let i = this.deps.length
    while (i--) {
        this.deps[i].depend()
    }
}
```

1. $mount: new _renderWatcher() (计算属性是有缓存的，会设置lazy:true, dirty=lazy)
2. _renderWatcher.get(): Dep.target = _renderWatcher,调用vm._update(vm._render(), hydrating)，render的时候会获取html中用到的响应式属性，上面例子中先用到了a,这时会触发a的get钩子,a_dep.subs.push(_renderWatcher)
2. 又获取到b, new _computedWatcher(): watcher.get(), pushTarget(_renderWachter)
3. _computedWatcher.evaluate(): watcher.get()
    - pushTarget(_computedWatcher),Dep.target = _computedWatcher
    - this.getter.call(vm, vm),会执行computed属性绑定的handler,即调用a+1,触发a的get钩子,调用dep.depend()
    - a_dep.subs.push(_computedWatcher), _computedWatcher.deps.push(a_dep)
    - popTarget(_computedWatcher), Dep.target = _renderWatcher
    - a_dep.subs = [_renderWatcher,_computedWatcher]  
    - 当a发生变化时，会遍历a的subs中的watcher调用update()方法。
4. _computedWatcher.depend()
    - watcher.depend()
    - deps.depend()
    - b_dep.subs.push(_renderWatcher), _renderWatcher.deps.push(b_dep)
    - 当计算属性的值修改之后会通知subs中的watcher调用update()。
5. a变化是，会调用_renderWatcher,_computedWatcher的updtae,如果是_computedWatcher的话不会重新执行一遍只会把this.dirty 设置成 true，如果数据变化的时候再执行watcher.evaluate()进行info更新，没有变化的的话this.dirty 就是false。这就是computed缓存机制。
6. watch 原来相似，但是会在声明时设置user = true, watcher.run()时，会调用cb



参考文献
1. [剖析 Vue.js 内部运行机制](https://juejin.cn/book/6844733705089449991/section/6844733705211084808)
2. [文献1 作者学习vue源码的仓库地址](https://github.com/answershuto/learnVue/tree/master/vue-src)
3. [深入解析Vue依赖收集原理](https://zhuanlan.zhihu.com/p/45081605)
4. [结合源码聊一聊Vue的响应式原理__Vue.js](https://vue-js.com/topic/5fd362c44590fe0031e594ed)
5. [vue响应式原理](https://segmentfault.com/a/1190000016333054)
6. [Vue2.1.7源码学习](http://hcysun.me/2017/03/03/Vue%E6%BA%90%E7%A0%81%E5%AD%A6%E4%B9%A0/)
7. [Vue源码笔记系列](https://www.yuque.com/chenshier/chuyi/epy88d)
