1. flex 设置flex-grow:1时，若此时未设置最小高度，当内容过多时，会穿透父元素高度
2. 滚动条挤压布局 及 el-scrollbar的使用
期望：滚动条出现，但是内容位置不变
- 如果有滚动条挤压布局的情况，要将滚动条宽度计算在内
- 页面为全屏时，外部包裹容器可用
当页面宽度过小时会有问题
```css
.wrap-outer{
  margin-right: calc(100% - 100vw); 
  /* 或 */
  margin-left: calc(100vw - 100%);
  /* 或 */
  padding-left: calc(100vw - 100%);
}
```
- overflow: overlay（兼容性不好）
- vue项目可以直接使用el-scrollbar
```css
.el-scrollbar {
  height: 100%;
  .el-scrollbar__wrap {
    overflow-x: hidden;
  }
}
```
原理：
```html
<style>
  .el-scroll {
    overflow: hidden;
    position: relative;
  }
  /* 高度与外层保持一致，设置可滚动，即overflow:scroll */
  /* 用户想要某个方向不滚动，显式设置即可，如overflow-x:hidden */
  .el-scrollbar__wrap {
    overflow: scroll;
    height: 100%;
  }
</style>
<!-- 设置高度后，确保外层高度小于内部要滚动内容 -->
<div class="el-scrollbar">
  <!-- 将原生滚动条挤压出外边框 -->
  <!-- 1. 添加自己的滚动条 -->
  <!-- 2. 可防止滚动条占用布局区域 -->
  <!-- 2-举例(如图)：项目中使用v-show切换tab，对应的具体内容有的有滚动条，有的没有，如果统一布局时，滚动条会占用整体宽度的一部分，没有滚动条的局部页面就会多一条滚动条宽度的空白区域 -->
  <div class="el-scrollbar__wrap" style="margin-bottom: -10px; margin-right: -10px;">
  <!-- 包裹滚动内容，实际高度会被滚动内容撑开 -->
    <div class="el-scrollbar__view"></div>
  </div>
  <!-- 模拟横向滚动条 -->
  <div class="el-scrollbar__bar is-horizontal">
    <div class="el-scrollbar__thumb" style="transform: translateX(0%);"><div>
  </div>
  <!-- 模拟纵向滚动条，transfrom -->
  <div class="el-scrollbar__bar is-vertical">
    <div class="el-scrollbar__thumb" style="transform: translateY(0%); height: 51.4894%;"></div>
  </div>
</div>
```
!(滚动条挤压布局)[./images/滚动条挤压布局.png]
3. 设置圆角，有部分线条不显示问题
- 第一种情况是内部元素有背景色但是未设置圆角，设置即可
- 第二种是原生滚动条造成，vue项目可以使用element-ui的`el-scroll`，如果使用原生滚动条可以增加外部容器，将圆角添加到外部容器上，并增加`overflow:hidden`
!(圆角不全效果图)[./images/圆角不全效果图.png]
4. element-ui form 验证多层嵌套数据
`要点`
1. 注意el-form-item的prop要传string 
2. 如果options为数组，可以写为`:prop="'options['+index+']'"`
3. 如果options为对象，还可以写为`:prop="'options.'+index+'.xx'"`
4. rules写到v-for元素中：:rules="{ required: true, message: '请选择'}"
5. v-modal部分：`v-model="templetDialogForm.options[index]"`，如果是对象，写成`v-model="selectedItem.xx"`
6. 如果需要动态新增，举例，如果options是数组，直接新增templetDialogForm.options的值就行：this.templetDialogForm.options.push("")
```html
<el-form :model="templetDialogForm" :rules="templetRules" ref="templetDialogForm">
  <div v-for="(selectedItem, index) in templetDialogForm.options" :key="selectedItem.name + '_' + index">
    <el-form-item
      :prop="'options['+index+'].value'"
      :label="selectedItem.text"
      :rules="{required:true,message:'请选择',trigger:'change'}"
    >
      <el-select v-model="selectedItem.value" placeholder="请选择">
        <el-option v-for="item in selectedItem.options" :key="item.value" :value="item.value" :label="item.name" :title="item.name">
        </el-option>
      </el-select>
    </el-form-item>
  </div>
</el-form>
<script>
templetDialogForm = {
  // ... 其他数据
  options: [
    { text: "内容分类:", value: "", options: this.contentList },
    { text: "典型代表:", value: "古典文学", options: this.typicalList },
    { text: "开本:", value: "正16开 185*260mm", options: this.formatList },
    { text: "元素:", value: "参考文献", options: this.elementList },
    { text: "书眉:", value: "无书眉", options: this.browList },
    { text: "页码:", value: "无页码", options: this.pageNumberList },
    { text: "正文分栏:", value: "有分栏", options: this.textColumnList },
    { text: "目录类型:", value: "传统", options: this.catalogTypeList },
  ],
},
</script>
```
