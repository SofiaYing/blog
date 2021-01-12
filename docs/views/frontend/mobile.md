[使用meta标签设置viewport](https://juejin.cn/post/6844903943298891790#heading-2)
layout viewport = ideal viewport / scale
无论计算出来的layout viewport的值大小如何，最后都会被浏览器自动缩放到与visual viewport大小相等，并不会出现滚动条
visual viewport = 375px, 设置scale值为0.5时，那么layout viewport的大小就是ideal viewport的两倍，即750px, 浏览器自动缩放到与visual viewport大小, 文本看起来就小了