---
title: vue运行机制
date: 2020-11-25
categories:
  - frond-end
tags :
  - open source project
  - vue
---
beforeCreate created
beforeCreate. 不能用props，methods，data，computed等。
initState. 初始化props，methods，data，computed等。
created. 此时已经有，props，methods，data，computed等，要用data属性则可以在这里调用。

在beforeCreate、created这俩个钩子函数执行的时候，并没有渲染 DOM，所以我们也不能够访问 DOM，一般来说，如果组件在加载的时候需要和后端有交互，放在这俩个钩子函数执行都可以，如果是需要访问 props、data 等数据的话，就需要使用 created 钩子函数

当 render function 被渲染的时候，因为会读取所需对象的值，所以会触发 getter 函数进行「依赖收集」，「依赖收集」的目的是将观察者 Watcher 对象存放到当前闭包中的订阅者 Dep 的 subs 中。

beforeMount mounted
在挂载开始之前被调用：相关的 render 函数首次被调用。

该钩子在服务器端渲染期间不被调用。

在执行 vm._render() 函数渲染 VNode 之前，执行了 beforeMount 钩子函数，在执行完 vm._update() 把 VNode patch 到真实 DOM 后，执行 mounted 钩子。


beforeUpdate、updated
beforeUpdate 和 updated 的钩子函数执行时机都应该是在数据更新的时候
这里有个细节是_isMounted, 表示要在mounted之后才执行beforeUpdate

至于updated则表示，当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作

Dep 中的每一个 Watcher

Dep：扮演观察目标的角色，每一个数据都会有Dep类实例，它内部有个subs队列，subs就是subscribers的意思，保存着依赖本数据的观察者，当本数据变更时，调用dep.notify()通知观察者
Watcher：扮演观察者的角色，进行观察者函数的包装处理。如render()函数，会被进行包装成一个Watcher实例 
Observer：辅助的可观测类，数组/对象通过它的转化，可成为可观测数据

```js
export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)
  if (opts.methods) initMethods(vm, opts.methods)
  if (opts.data) {
    initData(vm)
  } else {
    observe(vm._data = {}, true /* asRootData */)
  }
  if (opts.computed) initComputed(vm, opts.computed)
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
```

```js
  this._ob = observe(options.data)
  this._watchers = []
  this._watcher = new Watcher(this, render, this._update)
  this._update(this._watcher.value)
```

## 响应式原理 
涉及到的知识点
1. MVVM
2. typeof
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
    //在new一个Watcher对象时将该对象赋值Dep.target，在get中会用到 
    Dep.target = this
  }
  update() {
    //更新视图
  }

  // addDep (dep: Dep) {
  //   const id = dep.id
  //   if (!this.newDepIds.has(id)) {
  //     this.newDepIds.add(id)
  //     this.newDeps.push(dep)
  //     if (!this.depIds.has(id)) {
  //       dep.addSub(this)
  //     }
  //   }
  // }
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

  // 由于JavaScript是单线程模型，所以虽然有多个观察者函数，但是一个时刻内，就只会有一个观察者函数在执行，那么此刻正在执行的那个观察者函数，所对应的Watcher实例，便会被赋给Dep.target这一类变量，从而只要访问Dep.target就能知道当前的观察者是谁。
  /*依赖收集，当存在Dep.target的时候添加观察者对象*/
  // depend () {
  //   if (Dep.target) {
  //     Dep.target.addDep(this)
  //   }
  // }

}

Dep.target = null
```
```js
function defineReactive(obj,key,val) {
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



问：同一个属性多次触发get方法，每次都会dep.addSub(Dep.target)，push一个Watcher对象，后边notify的时候，就触发很多次update了。。。
答：实际上是有去重的逻辑的，这些逻辑对于理解原理是多余的所以我省略了。回答一下你的疑问：Watcher是有一个id属性的，每个Watcher的id是不一样的，用这个id就可以去重，在被push进去之前会先看看是否已经有相同id的Watcher存在即可～

问：1. defineReactive是data有多个属性就调用多少次吗，那这样的话，不是会导致生成多个Dep对象吗，好像不太合理啊
2.有个疑问，new Watcher的时候，把自己赋给Dep.target，是出于什么目的呢。这样还必须保证get依赖收集和对应的new出来的watcher的执行顺序，因为Dep.target是会被覆盖的
答：data是Obj的实际上是递归调用执行的，Dep.target你可以想象一下一个Watcher对象在多个Dep中的场景，可以参考vuex场景。

我的理解：
一个Vue实例（Vue组件）唯一拥有一个Watcher实例w；
一个data中的属性唯一拥有一个Dep实例dep；
w负责监听组件的data中被render到的属性的变化，因此一个Watcher实例可对应多个dep（代码中表现为同个Watcher实例存在于多个Dep实例的subs数组里）；
同时，如栗子2，同个属性可以被多个Vue实例拥有，即被多个Watcher实例监听，因此一个Dep实例可对应多个w（代码中表现为Dep实例的subs是个数组，可拥有多个Watcher实例）；
ps：作者可能是对源码做了比较多的细节丢弃，以期简洁，所以有些地方可能比较难懂，比如这份代码在栗子1是行得通的，栗子2好像行不通，我觉得可以忽略
例子2里边说的是两个Vue实例对一个data对象的响应，该对象的属性一修改，需要触发两个Vue实例都产生响应。所以场景体现的是一个data属性对应多个Vue实例，而Vue实例跟Watcher实例、data属性跟Dep实例，分别都是一对一的对应关系，自然这里体现的也是一个Dep实例对应多个Watcher实例了

我是这样理解的：
1: Dep不是订阅者，而是订阅者的容器，其背后是每个key（每个key有个Dep），用于放置订阅了此key的watcher们。
2: watcher不是观察者，而是“依赖于”，表示当前 组件/实例 使用了/依赖于 某个key。故当前实例只有一个watcher
3: 但是每个key可以被多个实例依赖（当发现某实例访问了此key后就是将那实例的watcher放入此key的订阅者容器Dep中表示那个实例订阅了这个key）
不知道对不对，欢迎交流

一个对象属性对应一个dep，一个dep对应多个watcher(一个对象属性可能再多个标签使用，那么就会有对应多个watcher，这些watcher都会放入到这个对象属性唯一对应的dep中)，这是Vue1.0的实现，但数据过大时，就会有很多个watcher，就会出现性能问题；所以在Vue2.0中引入的VDOM，给每个vue组件绑定一个watcher，这个组件上的数据的dep中都包含有该watcher，当该组件数据发生变化时，就会通知watcher触发update方法，生成VDOM，和旧的VDOM进行比较，更新改变的部分，极大的减少了watcher的数量，优化了性能；（所以，在Vue2.0中是一个组件对应一个watcher）
依楼上而言，1.0 版本是一个属性 Dep 的 subs 中包含多个用到该属性的 Watcher，2.0 版本中一个组件一个 Watcher，那么意味着该组件涉及的多个数据对应的 Dep subs 中，会至少包含同一个组件的 Watcher。不知理解是否正确
这个组件唯一对应的叫渲染 `watcher`

参考文献
1. [剖析 Vue.js 内部运行机制](https://juejin.cn/book/6844733705089449991/section/6844733705211084808)
2. [文献1 作者学习vue源码的仓库地址](https://github.com/answershuto/learnVue/tree/master/vue-src)
3. [深入解析Vue依赖收集原理](https://zhuanlan.zhihu.com/p/45081605)


我的猜想：
1. 一个key，对应一个dep
2. 第一次依赖收集的时候，会new Watcher()，Dep.target会指向watcher，之后再次读取，Dep.target会为null，所以不会再次收集。