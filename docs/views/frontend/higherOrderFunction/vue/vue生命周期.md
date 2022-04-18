---
title: vue生命周期
date: 2020-12-17
categories:
  - frond-end
tags :
  - open source project
  - vue
---

- beforeCreate // 调用该生命周期前已初始化生命周期，事件和渲染函数，不能访问到props等属性
- created // 调用该生命周期前已顺序初始化具体的数据—— injections => props => methods => data => computed => watch => initProvide
- beforeMount // 调用该生命周期前已初始化渲染函数$options.render
- mounted // 调用该生命周期前已渲染真实节点
- beforeUpdate // 状态改变时，会在nextTick中更新视图前调用
- updated // 已调用render函数重新渲染activated // keep-alive缓存组件渲染时调用 [首次加载时在 mounted 之后]deactivated // keep-alive缓存组件销毁后调用
- beforeDestroy // 实例销毁前调用
- destroyed // 实例销毁后调用errorCaptured


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
Vue.prototype._init = function ( options ) {
  var vm = this;
  // ...省略： 根据合并策略完成配置选项合并   //
  vm.$options = mergeOptions(
    resolveConstructorOptions( vm.constructor ),
    options || {},
    vm
  );

  vm._self = vm;
  initLifecycle( vm );  // 初始化$parent, $root, 生命周期相关属性 
  initEvents( vm );     // 挂载父组件传入的事件监听
  initRender( vm );     // 挂载$createElement, $attrs, $listener
  callHook( vm, 'beforeCreate' );
  initInjections( vm ); // 初始化 injections [在data 和 props 之前]
  initState( vm ); // 初始化 props methods data computed watch 属性
  initProvide( vm ); // 初始化 provide [在data 和 props 之后]
  callHook( vm, 'created' );

  if ( vm.$options.el ) { // 存在el时 主动挂载 否则 手动使用$mount
    vm.$mount( vm.$options.el );  // $mount主要工作：new了一个渲染Watcher，并将updateCompent作为callback传递进去并执行
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



参考文献
1. [从源码了解Vue生命周期](https://juejin.cn/post/6847902222949285901#heading-12)


