由于每个圆都不一样，所以都需要绘制相应的缓存圆。示例中的离屏渲染主要是在动画时，每次重绘不需要再重写绘制这个圆，只要获取cache中的，drawImage后改变位置即可

https://juejin.cn/post/6844903989197144078#heading-4
链接中的离屏渲染，雪花都是一样大小，new 一个cacheSnow就可以了

Canvas 的默认大小为300像素×150像素（宽×高，像素的单位是px）。可以通过JavaScript动态创建图像（ creates graphics on the fly。<canvas>元素可以像任何一个普通的图像一样（有margin，border，background等等属性）被设计。然而，这些样式不会影响在canvas中的实际图像。当开始时没有为canvas规定样式规则，其将会完全**透明**。

canvas 画布自适应方法
```js
//在需要重新适配的时机，进行监听
currentWidth = canvas.offsetWidth * dpr
currenHeight = canvas.offsetHeight * dpr

oldWidth= canvas.width;
oldHeight h = canvas.height;

//重置width,height会清空画布，所以需要将原画布保存下来
var newCanvas = document.createElement("canvas");
newCanvas.width = w;
newCanvas.height = h;
newCanvas.getContext("2d").drawImage(canvas, 0, 0);

canvas.width = currentWidth;
canvas.height = currenHeight;

context.drawImage(image, 0, 0, currentWidth, currenHeight);
```
### drawImage
- drawImage(image, dx, dy) 
- drawImage(image, dx, dy, dw, dh) 
- drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh)
![canvas drawImage 参数](../images/canvasDrawImage.png)

canvas save & restore
[理解Canvas Context 的save() 和 restore()](https://juejin.cn/post/6844903879599996942)

[Canvas中的save方法和restore方法](https://www.cnblogs.com/fangsmile/p/9530226.html)

[[源码阅读]基于Canvas+贝塞尔曲线算法的平滑手写板](https://segmentfault.com/a/1190000019514679)

[Vue 前端验证码 - canvas Rem布局自适应 - 跨域问题](cnblogs.com/Jlay/p/vue_canvas.html)

版本问题
rc.1版本
1.ios无丢图现象
2.图片以transform缩小到外框里显示时，会有问题，需去掉
3.注意overflow：hidden
4.line-height和字号大小相同，才能绘制出单行垂直居中的效果
rc.5版本 ios有丢图现象，textarea多行合成只垂直居中显示一行
rc.7版本，issue上作者回答问题提供
1.textarea/input能处理空格问题及多行文本
2.似乎也丢图，不好复现
目前解决方案：可编辑div，或者用div代替input/textarea进行显示
另：input/textaera有自有的padding，需要注意，还有box-sizing问题

微信头像跨域
html2canvas设置
useCORS: true,
存储头像的img设置
this.avatar.crossOrigin = 'anonymous'
this.avatar.src = data.headimgurl;

解决页面transform带来的绘制偏移
var transformLay = $("#swiper_container").css("transform");
var Lay = transformLay.replace(")", "").split(',');
$("#swiper_container").css({ "transform": "", "left": parseInt(Lay[4]), "top": parseInt(Lay[5]) });

https://segmentfault.com/a/1190000011478657
