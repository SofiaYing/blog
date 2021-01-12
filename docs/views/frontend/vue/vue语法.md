v-model
```js
// 在 JS 中修改 x 的值，input 输入框里也会随之改变。同样地，在页面中的 input 输入框内手动输入值，变量 x 的值也会随之改变。
<input v-model="inputValue"/>

//语法糖，相当于
//event.target 触发事件的元素
//event.currentTarget 绑定事件的元素
<input v-bind:value="inputValue" v-on:input="inputValue=$event.target.value"/>
```
```js
// 有时不需要实时记录结果，只需要记录最终输入的结果值就可以了
// input 的原生 DOM 事件中还有一个change 事件，该事件是在输入框失去焦点时 或 按下回车键时 执行的。v-model 里以.lazy 修饰符的方式切换至该监听模式。
<input v-model.lazy="inputValue"/>

//等价于
<input v-bind:value="inputValue" v-on:change="inputValue=$event.target.value"/>
```
自定义组件上的v-model
```js
<custom-input
  v-bind:value="inputValue"
  v-on:input="inputValue = $event"
>
</custon-input>
```
所以为了正常工作，子组件需要
```js
<div>
  <input v-bind:value="value" v-on:input="$emit('input',$event.target.value)">
</div>

props:{
  value:{
    value:String
  }
}
```