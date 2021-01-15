```js
  window.requestAnimationFrame = (function() {
    return window.requestAnimationFrame ||
      window.webkitRequestAnimationFrame ||
      window.mozRequestAnimationFrame ||
      window.oRequestAnimationFrame ||
      window.msRequestAnimationFrame ||
      function(callback) {
        window.setTimeout(callback, 1000 / 60)
      }
  })()
```

onmousedown
onmouseup
onmousemove
onmouseover: 鼠标经过事件
onmouseout：鼠标划出事件

box.onmousedown = function(e){
    console.log(e)
}

- clientX/clienY 双精度浮点数，点击位置距离当前body可视区域的x，y坐标
- pageX/pageY 基于文档的边缘，考虑页面滚动。举个例子，如果页面向右滚动 200px 并出现了滚动条，这部分在窗口之外，然后鼠标点击距离窗口左边 100px 的位置，pageX 所返回的值将是 300。
- screenX/screenY 点击位置距离当前电脑屏幕的x，y坐标
- offsetX/offsetY 相对于带有定位的父盒子的x，y坐标
- x/y 和screenX、screenY一样 
![如图](./images/mouseLocation.png)

touch事件中的touches、targetTouches和changedTouches   clientX/clientY screenX/screenY  pageX/pageY
- touches: 当前屏幕上所有触摸点的列表;
- targetTouches: 当前对象上所有触摸点的列表;
- changedTouches: 涉及当前(引发)事件的触摸点的列表

[书籍HTML5+JavaScript动画基础源码](https://github.com/lamberta/html5-animation)
