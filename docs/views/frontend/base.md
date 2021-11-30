---
title: 基础知识
date: 2020-12-23
categories:
  - frond-end
tags :
  - base
---
Web 概述
Web（World Wide Web）即全球广域网，也称为万维网。我们常说的Web端就是网页端
Web标准 
- 结构标准（HTML）：用于对网页元素进行整理和分类。
- 表现标准（CSS）：用于设置网页元素的版式、颜色、大小等外观样式。
- 行为标准（JS）：用于定义网页的交互和行为。
W3C 万维网联盟组织，用来制定web标准的机构（组织）。
为什么要遵循WEB标准呢？因为很多浏览器的浏览器内核不同，导致页面解析出来的效果可能会有差异，给开发者增加无谓的工作量。因此需要指定统一的标准。

浏览器
浏览器分成两部分：
1、渲染引擎（即：浏览器内核）：
用来解析 HTML与CSS。渲染引擎决定了浏览器如何显示网页的内容以及页面的格式信息。
渲染引擎是浏览器兼容性问题出现的根本原因。
2、JS 引擎
Gecko是Mozilla的浏览器引擎，在Firefox中使用，SpiderMonkey是Firefox的JavaScript引擎。

Apple为Safari浏览器创造了Webkit引擎，Webkit引擎内置了JavaScriptCore引擎。虽然Apple允许在IOS设备上可以使用其他的浏览器代替Safari，但所用通过App Store分发的浏览器必须使用Webkit引擎。例如，Opera Mini浏览器在IOS设备上使用Webkit引擎，而在其他设备上使用Blink引擎。

Google起初使用Webkit作为Chrome浏览器的引擎，后来以Webkit引擎为基础创造了Blink引擎，所有基于Chromium开源浏览器衍生的产品都使用blink引擎。而大名鼎鼎的V8引擎就是Chromium-based浏览器的JavaScript引擎。

Microsoft维护着自己的EdgeHTML引擎，作为老的Trident引擎的替代方案。新的Edge的浏览器已经开始使用Chromium的Blink引擎了，而EdgeHTML引擎只在window 10上的Universal Windows Platform中被使用。
随着Edge也加入了Blink的阵营，基本上Webkit内核及Webkit内核的衍生Blink已经统治了浏览器市场。
```js
-moz-     /* 火狐等使用Mozilla浏览器引擎的浏览器 */
-webkit-  /* Safari, 谷歌浏览器等使用Webkit引擎的浏览器 */
-o-       /* Opera浏览器(早期) */
-ms-      /* Internet Explorer (不一定) */ 
```

(前端入门)[https://github.com/qianguyihao/Web/]
## 遍历DOM
- document对象 表示任何在浏览器中载入的网页，并作为网页内容的入口，也就是DOM 树
- document.documentElement 是一个会返回文档对象（document）的根元素的只读属性（如HTML文档的 <html> 元素）
- document.body 返回当前文档中的<body>元素或者<frameset>元素
- document.head <head> 标签
**在 DOM 的世界中，null 就意味着“不存在”**
### 子节点：childNodes, firstChild, lastChild
- childNodes: 集合（**是一个类数组的可迭代对象**）列出了所有子节点(**直系的子元素**)，包括文本节点。`document.body.childNodes`
- firstChild: 访问第一个子元素的快捷方式
- lastChild: 访问最后一个子元素的快捷方式
  1. 集合
  - 可以用for...of迭代它（集合是可迭代的，提供了所需要的 Symbol.iterator 属性）。
  - 无法使用数组的方法，因为它不是一个数组。
  - 不要使用for...in, 因为会列举所有可枚举属性（注意这不是Set，Set的属性默认是不可枚举的）
  ```js
  let dom = document.documentElement.childNodes
  for (let p in dom) {
    console.log(p) 
  }
  //0 1 2 entries keys values forEach length item
  ```
  2. 只读：不能通过类似 childNodes[i] = ... 的操作来替换一个子节点。
  3. 实时：如果保留一个对 elem.childNodes 的引用，然后向 DOM 中添加/移除节点，那么这些节点的更新会自动出现在集合中。

(带你手写一个对象，深入理解可迭代对象是什么，与类数组有什么区别)[https://blog.csdn.net/LeviDing/article/details/109793189]
### 兄弟节点Sibling和父节点
- 下一个兄弟节点：nextSibling
- 上一个兄弟节点：prevSibling
- 父节点：parentNode

### 纯元素导航
仅返回代表标签的和形成页面结构的元素节点。（如childNodes是包含所有节点的，如文本节点，元素节点，甚至如果注释节点存在的话，也能访问到。）
- children — 仅那些作为元素节点的子代的节点。
- firstElementChild，lastElementChild — 第一个和最后一个子元素。
- previousElementSibling，nextElementSibling — 兄弟元素。
- parentElement — 父元素。
```js
//html元素的父节点是document，但document不是元素类型的节点
document.documentElement.parentNode  // document
document.documentElement.parentElement  // null

// 当我们想从任意节点 ele 到 <html> 而不是到 document 时，这个细节可能很有用
while( ele = ele.parentElement){  // 向上，直到html
  // do something
}
```
## 搜索一个元素
- document.getElementById(id)
- **document.querySelector(css)** 相当于 document.querySelectorAll(css)[0]
- **document.querySelectorAll(css)**
- elem.closest(css) 查找与 CSS 选择器匹配的最近的祖先, elem自己也会被搜索
- 更建议使用document.querySelector: 以下都是实时的
  - elem.getElementsByTagName(tag)
  - document.getElementsByName(name)
  - elem.getElementsByClassName(className)

实时的（live）集合: 这样的集合始终反映的是文档的当前状态，并且在文档发生更改时会“自动更新”。
在下面的例子中，有两个脚本。
第一个创建了对 <div> 的集合的引用。截至目前，它的长度是 1。
第二个脚本在浏览器再遇到一个 <div> 时运行，所以它的长度是 2。
```html
<div>First div</div>

<script>
  //非实时的
  let queryDivs = document.querySelectorAll('div');
  alert(divs.length); // 1

  //实时的
  let getDivs = document.getElementsByTagName('div');
  alert(divs.length); // 1
</script>

<div>Second div</div>

<script>
  alert(queryDivs.length); // 1
  alert(getDivs.length); // 2
</script>
```
### 其他
nodeName/tagName： 用于元素名，标签名（除了 XML 模式，都要大写）。对于非元素节点，nodeName 描述了它是什么。只读。

attribute 大小写不敏感
- elem.hasAttribute(name) — 检查特性是否存在。
- elem.getAttribute(name) — 获取这个特性值。
- elem.setAttribute(name, value) — 设置这个特性值。
- elem.removeAttribute(name) — 移除这个特性。
- elem.attributes — 读取所有特性


box-sizing
- content-box (default) 所设置的 width 与 height 只会应用到这个元素的内容区
- border-box 设置的边框和内边距的值是包含在width内的(不包含margin)