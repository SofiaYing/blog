---
title: vue运行机制
date: 2020-11-25
categories:
  - frond-end
tags :
  - open source project
  - vue
---

- 在第一遍数据初始化中，执行new Vue()操作后会执行initState初始化用户传入的data，observe(data)为data添加响应式。
beforeCreate created
beforeCreate: 不能用props，methods，data，computed等。
initState. 初始化props，methods，data，computed等。
created: 此时已经有，props，methods，data，computed等，要用data属性则可以在这里调用。
在beforeCreate、created这俩个钩子函数执行的时候，并没有渲染 DOM，所以我们也不能够访问 DOM，一般来说，如果组件在加载的时候需要和后端有交互，放在这俩个钩子函数执行都可以，如果是需要访问 props、data 等数据的话，就需要使用 created 钩子函数

- 依赖收集的触发是在执行render之前，会创建一个渲染Watcher
在渲染Watcher创建时 mountComponent 内会new Watcher(),会将Dep.target指向自身并触发updateComponent,也就是执行_render生成VNode并执行_update将VNode渲染成真实DOM，在render过程中会对模板进行编译，此时就会对data进行访问从而触发getter，(当 render function 被渲染的时候，因为会读取所需对象的值，所以会触发 getter 函数进行「依赖收集」，「依赖收集」的目的是将观察者 Watcher 对象存放到当前闭包中的订阅者 Dep 的 subs 中。)由于此时Dep.target已经指向了渲染Watcher，接着渲染Watcher会执行自身的addDep，做一些去重判断然后执行dep.addSub(this)将自身push到属性对应的dep.subs中,同一个属性只会被添加一次，表示数据在当前Watcher中被引用。
当_render结束后，会执行popTarget()，将当前Dep.target回退到上一轮的指，最终又回到了null，也就是所有收集已完毕。之后执行cleanupDeps()将上一轮不需要的依赖清除。当数据变化是，触发setter，执行对应Watcher的update属性，去执行get方法又重新将Dep.target指向当前执行的Watcher触发该Watcher的更新。

　　这里可以看到有deps,newDeps两个依赖表，也就是上一轮的依赖和最新的依赖，这两个依赖表主要是用来做依赖清除的。但在addDep中可以看到if (!this.newDepIds.has(id))已经对收集的依赖进行了唯一性判断，不收集重复的数据依赖。为何又要在cleanupDeps中再作一次判断呢？
在cleanupDeps中主要清除上一轮中的依赖在新一轮中没有重新收集的，也就是数据刷新后某些数据不再被渲染出来了

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

Watcher
1. 传入 组件实例 观察者函数 回调函数 选项
2. 进行初始求值 watcher.get() 
   - 初始准备工作：将当前watcher赋值给Dep.target，清空newDeps newDepIds
   - 调用 观察者函数 进行计算、事后清理
    （1）计算：由于数据观测阶段执行了defineReactive()，所以计算过程用到的数据会得以访问，从而触发数据的getter，从而执行watcher.addDep()方法，将特定的数据记为依赖。
      - 对每个数据执行watcher.addDep(dep)后，数据对应的dep如果在newDeps里不存在，就会加入到newDeps里，这是因为一次计算过程数据有可能被多次使用，但是同样的依赖只能收集一次。并且如果在deps不存在，表示上一轮计算中，当前watcher未依赖过某个数据，那个数据相应的dep.subs里也不存在当前watcher，所以要将当前watcher加入到数据的dep.subs里
    （2）事后清理：



render()=>getter=>dep.depend()=>wacher.addDep(去除重复依赖)
比如页面中有多处调用了data中的name数据，就会出现多次name的watcher

将观察者Watcher实例赋值给全局的Dep.target，然后触发render操作只有被Dep.target标记过的才会进行依赖收集。有Dep.target的对象会将Watcher的实例push到subs中，在对象被修改触发setter操作的时候dep会调用subs中的Watcher实例的update方法进行渲染。


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
    Dep.target = this
    //会调用get
  }
  update() {
    //更新视图
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
}

Dep.target = null
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
      if (!this.depIds.has(id)) {
        dep.addSub(this)
      }
    }
  }

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
  // Object.defineProperty()里的get/set方法相对于var dep = new Dep()形成了闭包，从而很巧妙地保存了dep实例
  // 存储每个属性关联的watcher队列，当setter触发时依然能访问到
  const dep = new Dep()

  //如果属性为对象也创建相应observer
  let childOb = observe(val)

  Object.defineProperty(obj,key,{
    enumerable: true, 
    configurable: true,
    get: function reactiveGetter() {
      dep.addSub(Dep.target)
      // 收集依赖不直接使用addSub是为了能让Watcher创建时自动将自己添加到dep.subs中，这样只有当数据被访问时才会进行依赖收集，可以避免一些不必要的依赖收集。

      // if (Dep.target) {
      //   dep.depend() //将当前dep传到对应watcher中再执行watcher.addDep将watcher添加到当前dep.subs中
      //   if (childOb) {  //如果属性是对象则继续收集依赖
      //     childOb.dep.depend()
      //     ...
      //   }
      // }
      // return value
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
4. https://www.cnblogs.com/cjw-ryh/p/Vue.html
