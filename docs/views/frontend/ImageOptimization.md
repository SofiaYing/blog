---
title: note
date: 2020-12-11
categories:
  - frond-end
tags: 
  - performance optimization
---

JPEG/JPG PNG WebP Base64 SVG

### JPEG/JPG 
关键字：有损压缩、体积小、加载快、不支持透明
- 优点
压缩前后的质量损耗并不容易被我们人类的肉眼所察觉
最醒目、最庞大的图片，一定是以 .jpg 为后缀的
JPG 适用于呈现色彩丰富的图片，在我们日常开发中，JPG 图片经常作为大的背景图、轮播图或 Banner 图出现。
- 缺点
有损压缩在上文所展示的轮播图上确实很难露出马脚，但当它处理矢量图形和 Logo 等线条感较强、颜色对比强烈的图像时，人为压缩导致的图片模糊会相当明显。

此外，JPEG 图像不支持透明度处理，透明图片需要召唤 PNG 来呈现。

### PNG-8 与 PNG-24
关键字：无损压缩、质量高、体积大、支持透明

### Base64
和雪碧图一样，Base64 图片的出现，也是为了减少加载网页图片时对服务器的请求次数，从而提升网页性能。Base64 是作为雪碧图的补充而存在的。

在传输非常小的图片的时候，Base64 带来的文件体积膨胀、以及浏览器解析 Base64 的时间开销，与它节省掉的 HTTP 请求开销相比，可以忽略不计，这时候才能真正体现出它在性能方面的优势。

### WebP

由服务器根据 HTTP 请求头部的 Accept 字段来决定返回什么格式的图片。