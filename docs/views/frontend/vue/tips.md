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
3. 向子组件传值时是否使用v-bind:
结论：只有传递字符串常量时，不采用v-bind形式，其余情况均采用v-bind形式传递
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