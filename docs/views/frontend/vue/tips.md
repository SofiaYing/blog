1. 用scoped属性实现组件样式私有化的时候,如何对子组件（如引入了element-ui）设置样式 https://vue-loader.vuejs.org/zh/guide/scoped-css.html
2. element-ui 表单验证：必须保证自定义校验规则的每个分支都调用了callback方法，否则会导致el-form组件的validate方法无法进入回调函数。
```js
//自定义校验规则
var checkAge = (rule, value, callback) => {
  if (!value) {
    return callback(new Error('年龄不能为空'));
  }
  if (value < 18) {
    callback(new Error('必须年满18岁')); 
  } else {
    // 此分支必须 
    callback();
  }
};
```
3. 向子组件传值时是否使用`v-bind`
即便对象是静态的，我们仍然需要 `v-bind` 来告诉 Vue ,这是一个 JavaScript 表达式而不是一个字符串。
结论：只有传递字符串常量时，不采用`v-bind`形式，其余情况均采用`v-bind`形式传递
```html
<!-- 此时value为数值，计算1+value的值为25 -->
<blog-post v-bind:value="24"></blog-post>
<!-- 此时value为字符串，计算1+value的值为124 -->
<blog-post value="24"></blog-post>

<!-- 此时value为布尔值 -->
<blog-post v-bind:value="false"></blog-post>
<!-- 此时value为字符串 -->
<blog-post value="false"></blog-post>
```

4. 通过props向子组件传值，父组件修改后，子组件为何不更新？
**情况1：**
子组件将父组件传来的值，保存在本地，子组件使用v-model,此时想要响应props的变化，需在子组件内部添加watch
```html
<template>
  <el-input
    type="text"
    v-model="inputValue"
    @change="handleInputChange"
  >
  </el-input>
</template>

<script>
export default {
  name: "inputNumber",
  props: {
    number: [Number, String],
  },
  data() {
    return {
      inputValue: 0,
    };
  },
  watch: {
    number: {
      handler(newValue, oldValue) {
        this.inputNumber = newValue;
      },
    },
  },
};
</script>
```
**情况2：**
如下例所示，引用了子组件（情况1），按照需求，初始化number为1，父组件内部一定要声明change时间，事实拿到子组件number的最新值（如修改为2），这样在父组件修改number为1后，才能触发子组件的watch，不然在父组件内部的number始终为1，是没有修改过的。
```html
<template>
  <div class="selectAndInput">
    <el-select class="select" v-model="selectedValue" placeholder="请选择" :disabled="disabled" @change="selectedChange($event)">
      <el-option v-for="item in selectedOptions" :key="item.value" :value="item.value" :label="item.name"> </el-option>
    </el-select>
    <input-number
      :number="number"
      :min="min"
      :max="max"
      :unit="unit"
      :width="width"
      @change="handleInputChange"
      :disabled="disabled || selectedValue === 'none'"
    ></input-number>
  </div>
</template>

<script>
import inputNumber from "./inputNumber.vue";

export default {
  name: "selectAndInput",
  components: {
    inputNumber,
  },
  props: {
    type: {
      type: String,
      default: "无",
    },
    inputOptions: {
      type: Object,
      default: {},
    },
    disabled: {
      type: Boolean,
      default: false,
    },
  },
  watch: {
    type(newVal) {
      this.selectedValue = newVal;
    },
    inputOptions(newVal) {
      this.number = newVal.number;
    }
  },
  data() {
    return {
      number: this.inputOptions.number || 1,
      min: this.inputOptions.min || 1,
      max: this.inputOptions.max || 100,
      unit: this.inputOptions.unit || "行",
      width: this.inputOptions.width || 90,
      selectedValue: this.type,
      groupValue: { vAdjuestMode: this.type, vAdjuestHeight: this.inputOptions.number || 1 },
      selectedOptions: [
        { name: "无", value: "none" },
        { name: "居中", value: "center" },
        { name: "居上", value: "top" },
        { name: "居下", value: "bottom" },
        { name: "撑满", value: "justify" },
        { name: "均匀撑满", value: "justifyEven" },
      ],
    };
  },
  methods: {
    selectedChange(value) {
      if (value === "none") {
        this.number = 1;
      }
      this.groupValue = { vAdjuestMode: this.selectedValue, vAdjuestHeight: this.number };
      this.$emit("change", this.groupValue);
    },
    handleInputChange(value) {
      this.number = value;
      this.groupValue = { vAdjuestMode: this.selectedValue, vAdjuestHeight: this.number };
      this.$emit("change", this.groupValue);
    },
  },
};
</script>
```
5. 子组件修改父组件的值
传递的为简单数据类型
```html
<!-- 父组件 -->
<children-input :value.sync="value"></children-input>

<!-- 子组件 -->
<el-input v-model="inputValue"></el-input>
<script>
props: ["value"],
computed: {
  inputValue: {
    get() {
      return this.value
    },
    set(val) {
      this.$emit('update:value', val)
    }
  }
}
</script>
```
传递的为引用数据类型时，不需要
```html
<!-- 父组件 -->
<children-input :value="inputObj"></children-input>

<!-- 子组件 -->
<el-input v-model="inputObj.value"></el-input>
<script>
props: ["inputObj"]
</script>
```
6. el-dialog 触发生命周期问题
```html
<!-- 这样写可以触发生命周期，但是同时关闭多个el-dialog时，可能会出现报错(v-if 与 v-show同时使用) -->
<!-- [Vue warn]: Error in nextTick: “NotFoundError: Failed to execute ‘insertBefore‘ on 'Node' -->
<el-dialog v-if="xxx" :visible.sync="xxx"></el-dialog>

<!-- 可以增加父元素 （v-if 和 v-for也会出现问题）-->
<div v-if="xxx">
  <el-dialog :visible.sync="xxx"></el-dialog>
</div>
```
7. 记录一个视图不更新的问题
问题描述：上传图片后对vuex内的对象data的img属性进行更新，首次视图更新成功，再次失败。
原因： 页面内有ajax请求，会对data进行重新赋值，由于img是临时属性，所以接口返回的
data内是没有img属性的，虽然vuex内声明了默认值，但是ajax请求内数据已经被覆盖，
img已经丢失，也就没有绑定监听，所以视图更新失败。
解决办法：请求赋值时，添加img属性即可。

8. 如何根据环境配置不同路由
```js
// 1. 增加相应环境配置文件 .env.static
NODE_ENV=development
VUE_APP_PROJECT_NAME=static

// 2. package.json 增加相应命令
"scripts": {
  "serve": "vue-cli-service serve",
  "build": "vue-cli-service build",
  "lint": "vue-cli-service lint",
  "serve:static": "vue-cli-service serve --mode static",
  "build:static": "vue-cli-service build --mode static"
},

// 3. main.js 修改路由引用路径
import router from './router/PROJECT_NAME.js'

// 4. vue.config.js 增加重命名配置
const path = require('path')
const webpack = require('webpack')
let projectName = process.env.VUE_APP_PROJECT_NAME === 'static' ? 'staticIndex' : 'index'

module.exports = {
  publicPath: './',
  outputDir: process.env.VUE_APP_PROJECT_NAME === 'static' ? 'staticDist' : 'dist',
  productionSourceMap: process.env.NODE_ENV === 'development' ? true : false,
  configureWebpack: {
    plugins: [
      new webpack.NormalModuleReplacementPlugin(/(.*)PROJECT_NAME(\.*)/, function(resourse) {
        resourse.request = resourse.request.replace(/PROJECT_NAME/, `${projectName}`)
      })
    ]
  },
}

// 5. router文件夹下增加相应路由入口文件 staticIndex.js
```