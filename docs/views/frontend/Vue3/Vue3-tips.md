---
title: Vue3-tips
date: 2023-06-12
categories:
  - frond-end
tags :
  - Vue
  - Vue3
---
# Vue2 vs Vue3
1. Vue3的Template支持**多个根标签**
2. Vue3 createApp(组件), Vue2 new Vue({template,render})
# 找不到模块xxx.vue
原因：TypeScript 只能理解.ts文件
解决方法：Google搜索 Vue3 can not find module
创建xxx.d.ts文件, 告诉TS如何理解.vue文件
```js
declare module '*.vue' {
  import { ComponentOptions } from "vue"
  const componentOptions: ComponentOptions
  export default componentOptions
}
```
