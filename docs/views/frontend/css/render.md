---
title: render
date: 2022-08-08
categories:
  - frond-end
tags :
  - css
---
## 渲染引擎
- Google Chrome：Webkit(前期)、Blink(后期)
- Apple Safari：Webkit
- Mozilla Firefox：Gecko
- ASA Opera：Presto(前期)、Blink(后期)
- Microsoft IExplorer：Trident
- Microsoft Edge：Trident(前期)、Blink(后期)
## 渲染过程
1. 解析文件
HTML - HTML parser - DOM tree(document Object Model)
Style Sheets - CSS parser - CSSOM tree(CSS Object Model)
DOM tree + CSSOM tree => render tree
2. 绘制图层
- 回流:改变几何属性的渲染
- 重绘：改变外观属性而不影响几何属性的渲染
回流必定引发重绘，重绘不一定引发回流。
3. 合成图层
