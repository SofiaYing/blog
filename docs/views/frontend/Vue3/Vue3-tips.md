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

### v-model

因为父组件向子组件传递一个值，子组件触发父组件方法，通知其需要改变传入的值，这个过程太过于常见，Vue 提供了语法糖 v-model 来简化这一过程。

v-model 双向绑定，其实就是自动监听。

- 简化以下过程

```vue
// 父组件
<template>
  <Switch :value="state" @update:value="state = $event"></Switch>
</template>
<!-- ...  -->

//子组件
<!-- ... -->
<script>
const props = defineProps({
  value: Boolean,
});
const toggle = () => {
  emit("update:value", !props.value); //update: 后面接的必须是要监听的属性名
};
</script>
```

- 使用 v-model 后

```vue
// 父组件
<template>
  <Switch v-model:value="state"></Switch>
</template>
<!-- ...  -->
//子组件
<!-- ... -->
<script>
const props = defineProps({
  value: Boolean,
});
const toggle = () => {
  emit("update:value", !props.value); //update: 后面接的必须是要监听的属性名
};
</script>
```

::: details vue2 中的 v-model 和 .sync VS vue3 中的 v-model

- .sync 是 Vue 中的一个语法糖，用于简化父组件与子组件之间双向绑定数据的操作。原理：当父组件使用 .sync 修饰子组件的 prop 时，Vue 会为子组件自动添加一个名为 `update:<propName>` 的事件监听器，并在子组件触发该事件时更新父组件中相应的属性。用法：

```vue
// 在父组件中：
<template>
  <child-component :foo.sync="bar" />
</template>

<script>
import ChildComponent from "./ChildComponent.vue";

export default {
  components: {
    ChildComponent,
  },
  data() {
    return {
      bar: "hello",
    };
  },
};
</script>
```

```vue
// 在子组件中：
<template>
  <button @click="updateFoo">Click me</button>
</template>

<script>
export default {
  props: ["foo"],
  methods: {
    updateFoo() {
      this.$emit("update:foo", "world");
    },
  },
};
</script>
```

这样，当在子组件中点击按钮时，会触发 updateFoo 方法，该方法通过 \$emit('update:foo', 'world') 发送一个事件，父组件监听到这个事件，并更新 bar 属性的值为 'world'。

- 在 Vue 2 中，v-model 是一个指令，用于在表单元素和自定义组件中创建双向数据绑定。它的原理是通过监听输入事件和更新数据属性来实现数据的双向绑定。使用方法：

```vue
<!-- 在表单元素中使用： -->
<input v-model="message" type="text">
```

这样做会将输入框的值与 Vue 实例中的 message 属性双向绑定起来。当输入框的值改变时，message 的值也会跟着改变，并且当 message 的值改变时，输入框的值也会自动更新。

```vue
<!-- 在自定义组件中使用： -->
<custom-input v-model="message"></custom-input>
```

假设 custom-input 是一个自定义组件，通过在组件上使用 v-model，可以将组件内部的值与外部的数据进行双向绑定。在组件内部，通过接收 value 属性来获取外部的值，并通过触发 input 事件来更新外部的值。

```vue
// custom-input 组件内部
<template>
  <input :value="value" @input="$emit('input', $event.target.value)" />
</template>

<script>
export default {
  props: ["value"],
};
</script>
```

这样，当在父组件中使用 custom-input 组件时，就可以像使用原生的表单元素一样，实现双向数据绑定。

- 在 Vue 3 中，v-model 仍然存在，但是它不再是一个指令，而是一个由开发者定义的指令或组件的 API。Vue 3 引入了 v-model 的模型选择器，允许开发者自定义组件上的 v-model 行为。使用方法：

```vue
<!-- 在内置组件中使用： -->
<input v-model:modelValue="message" type="text">
```

在 Vue 3 中，使用 v-model 指令需要指定一个模型选择器，通常是 :modelValue。这样做会将输入框的值与 Vue 实例中的 message 属性双向绑定起来。

```vue
<!-- 在自定义组件中使用： -->
<custom-input v-model:modelValue="message"></custom-input>
```

与内置组件类似，当在自定义组件上使用 v-model 时，需要指定一个模型选择器，通常是 :modelValue。在组件内部，通过接收 modelValue 属性来获取外部的值，并通过触发 update:modelValue 事件来更新外部的值。

```vue
// custom-input 组件内部
<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>

<script>
export default {
  props: ["modelValue"],
};
</script>
```

这样，当在父组件中使用 custom-input 组件时，就可以像使用原生的表单元素一样，实现双向数据绑定。
:::

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
