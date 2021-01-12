由于每个圆都不一样，所以都需要绘制相应的缓存圆。示例中的离屏渲染主要是在动画时，每次重绘不需要再重写绘制这个圆，只要获取cache中的，drawImage后改变位置即可

https://juejin.cn/post/6844903989197144078#heading-4
链接中的离屏渲染，雪花都是一样大小，new 一个cacheSnow就可以了
[理解Canvas Context 的save() 和 restore()](https://juejin.cn/post/6844903879599996942)