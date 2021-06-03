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

- document 接口表示任何在浏览器中载入的网页，并作为网页内容的入口，也就是DOM 树
- document.body 返回当前文档中的<body>元素或者<frameset>元素
- document.documentElement 是一个会返回文档对象（document）的根元素的只读属性（如HTML文档的 <html> 元素）

box-sizing
- content-box (default) 所设置的 width 与 height 只会应用到这个元素的内容区
- border-box 设置的边框和内边距的值是包含在width内的(不包含margin)