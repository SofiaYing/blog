---
title: render
date: 2022-08-08
categories:
  - frond-end
tags :
  - css
---
浏览器三大核心内容：渲染引擎、渲染过程、兼容性
## 渲染引擎
- Google Chrome：Webkit(前期)、Blink(后期)
- Apple Safari：Webkit
- Mozilla Firefox：Gecko
- ASA Opera：Presto(前期)、Blink(后期)
- Microsoft IExplorer：Trident
- Microsoft Edge：Trident(前期)、Blink(后期)
## 渲染过程
### 1. 解析文件
HTML - HTML parser - DOM tree(document Object Model)
Style Sheets - CSS parser - CSSOM tree(CSS Object Model)
DOM tree + CSSOM tree => render tree
### 2. 绘制图层
- 回流(reflow):改变几何属性的渲染
- 重绘(repaint)：改变外观属性而不影响几何属性的渲染
回流必定引发重绘，重绘不一定引发回流。

避免规则层级过多
浏览器的CSS解析器解析css文件时，对CSS规则是**从右到左**匹配查找，样式层级过多会影响回流重绘效率，建议保持CSS规则在3层左右。
不要限定id选择符、类选择符；规则越具体越好；尽量不要使用标签；依靠继承

will-change
requestAnimationFrame
### 3. 合成图层
## 兼容性 - 待补充

1. [属性在渲染时产生的影响](https://csstriggers.com)
2. [Understanding Reflow and Repaint in the browser](https://dev.to/gopal1996/understanding-reflow-and-repaint-in-the-browser-1jbg)

Event loops 浏览器的事件循环?????????(https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model)
待细看问题：浏览器的渲染过程

