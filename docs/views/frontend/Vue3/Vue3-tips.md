---
title: Vue3
date: 2024-03-14
categories:
  - frond-end
tags:
  - Vue
  - Vue3
---

# Vue2 vs Vue3

1. Vue3 的 Template 支持**多个根标签**
2. Vue3 createApp(组件), Vue2 new Vue({template,render})

# 找不到模块 xxx.vue

原因：TypeScript 只能理解.ts 文件
解决方法：Google 搜索 Vue3 can not find module
创建 xxx.d.ts 文件, 告诉 TS 如何理解.vue 文件

```js
declare module '*.vue' {
  import { ComponentOptions } from "vue"
  const componentOptions: ComponentOptions
  export default componentOptions
}
```

## 选项式 API(Options API) 与 组合式 API(Composition API)

https://juejin.cn/post/6985493062432587813?searchId=2024032616434345ED9C15163DE0076C2B

## 父子组件通信

1. setup()

```vue
// 父组件
<template>
  <!-- $event 触发该事件时传递的参数 -->
  <Switch :value="state" @input="state = $event"></Switch>
</template>
<script lang="ts">
import { ref } from "vue";
import Switch from "../lib/Switch.vue";
export default {
  setup() {
    const state = ref(false);
    return { state };
  },
  components: { Switch },
};
</script>
//子组件
<template>
  <div role="switch" class="zyy-switch zyy-switch-wrapper" @click="toggle">
    <div class="zyy-switch-content" :class="{ checked: value }"></div>
  </div>
</template>
<script lang="ts">
export default {
  props: {
    value: Boolean,
  },
  setup(props, context) {
    const toggle = () => {
      // this.$emit
      console.log(!props.value);
      context.emit("input", !props.value);
    };

    return { toggle }; //return {toggle: toggle} ES6 简化
  },
};
</script>
```

2. setup 语法糖

```vue
// 父组件
<template>
  <!-- $event 触发该事件时传递的参数 -->
  <Switch :value="state" @input="state = $event"></Switch>
</template>
<script setup lang="ts">
import { ref } from "vue";
import Switch from "../lib/Switch.vue";

const state = ref(false);
</script>
//子组件
<template>
  <div role="switch" class="zyy-switch zyy-switch-wrapper" @click="toggle">
    <div class="zyy-switch-content" :class="{ checked: value }"></div>
  </div>
</template>
<script setup lang="ts">
const props = defineProps({
  value: Boolean,
});
const emit = defineEmits();
const toggle = () => {
  emit("input", !props.value);
};
</script>
```

## What is Vue?

Vue is a JavaScript **framework** for building **user interfaces**. It **builds on top of** standard HTML, CSS, and JavaScript and provides a declarative, component-based programming model that helps you efficiently develop user interface, **be they simple or complex**.

### two core features of Vue:

Declarative Rendering 声明式渲染: Vue extends standard HTML with a template syntax that allows us to declaratively describe HTML output based on JavaScript state.

Reactivity 响应性: Vue automatically tracks JavaScript state changes and efficiently updates the DOM when changes happen.

一些基础知识补充：

- framework 框架
- interface 界面〔指计算机屏幕布局〕
- GUI (graphical user interface) 〔计算机的〕图形用户界面
- builds on top of 建立在...基础上
- declarative adj. 声明式的 declare v. 声明 declaration n. 声明
- be they simple or complex 无论是简单的还是复杂的
  ::: details 例句
  1. When they come to other doors in life, be they real or metaphorical, they won't hesitate to open them and walk through.  
     当他们遇到生活中的其他门时，或真实或隐喻，他们将毫不犹豫地打开门，走过去。
  2. Tears, be they of sorrow, anger, or joy, typically make Americans feel uncomforuble and embarrassed.  
     不管是悲伤的、愤怒的还是喜悦的眼泪，都会让美国人感到不舒服和尴尬。
  3. Everyone, be they doctors or central bankers or politicians, makes mistakes.
     每个人都会犯错，不论是医生、央行银行家或是政客。
- 编程框架：是一种软件架构，它提供了一系列工具、函数和库，用于简化和加速软件开发过程。
- 声明：指的是明确、正式陈述或宣布某种观点、立场、意图或事实的行为或文件

### The Progressive Framework

it's a framework that can grow with you and adapt to your needs.

补充知识
生态： https://www.zhihu.com/question/461838525/answer/1911145850

### Single-File Components 单文件组件

In most build-tool-enabled Vue projects, we author Vue components using an HTML-like file format called Single-File Component (also known as \*.vue files, abbreviated as SFC). A Vue SFC, as the name suggests, encapsulates the component's logic (JavaScript), template (HTML), and styles (CSS) in a single file.

```
<script setup>
import {ref} from 'vue'
const count = ref(0)
</script>

<templete>
  <button @click="count++"> Count is: {{count}}</button>
</templete>

<style>...</style>
```

## 新建一个项目

### alias

1. yarn add @types/node -D
2. vite.config.ts

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import { resolve } from "path";

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      "@": resolve(__dirname, "./src"),
    },
  },
});
```

3. tsconfig.json

```json
{
  "compilerOptions": {
    // ...
    "baseUrl": "./",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```
