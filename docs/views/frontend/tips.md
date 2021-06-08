1. audio chrome出于安全考虑 是不允许audio自动播放的，需要相应的配置
2. 微信webview scroll bounce 解决办法
```js
var overscroll = function(el) {
    el.addEventListener('touchstart', function() {
        var top = el.scrollTop,
            totalScroll = el.scrollHeight,
            currentScroll = top + el.offsetHeight;
        //If we're at the top or the bottom of the containers
        //scroll, push up or down one pixel.
        //
        //this prevents the scroll from "passing through" to
        //the body.
        console.log('11', el, el.scrollHeight, el.scrollTop)
        if (top === 0) {
            el.scrollTop = 1;
        } else if (currentScroll === totalScroll) {
            el.scrollTop = top - 1;
        }
    });
    el.addEventListener('touchmove', function(evt) {
        //if the content is actually scrollable, i.e. the content is long enough
        //that scrolling can occur
        if (el.offsetHeight < el.scrollHeight)
            evt._isScroller = true;
        console.log('22', evt, el, el.offsetHeight)
    });
}
overscroll(document.querySelector('#divpar'));
document.body.addEventListener('touchmove', function(evt) {
    //In this case, the default behavior is scrolling the body, which
    //would result in an overflow.  Since we don't want that, we preventDefault.
    console.log('333')
    if (!evt._isScroller) {
        console.log('444')
        evt.preventDefault();
    }
});
```
3. 对象键值对为变量
可：arr[index] = {[i]:j}
可：arr[index][''+i+'']=j
不可：arr[index]={i:j}     或    arr[index][i]=j

4.transform matrix=>
var transformStr = $(self.el).css("transform");
var angle = 0;
​
if (transformStr !== "" && transformStr !== "none") {
   var _transformStr = transformStr.substring(7, transformStr.length - 1);
   var transformArray = _transformStr.split(',');
   if (transformArray.length === 6) {
        var a = transformArray[0];
        var b = transformArray[1];
        var c = transformArray[2];
        var d = transformArray[3];
        var scale = Math.sqrt(a * a + b * b);
        var sin = b / scale;
        var angle = Math.round(Math.atan2(b, a) * (180 / Math.PI));
   }
}
5.gif重置可以通过清空src，再赋值做到，注意同一时间反复读取同一个，状态可能不会更新
6.微信保护机制：比如微信浏览器内打开的网页内有iframe，点击iframe嵌套的网页内的<a>再跳转的网页，微信是无法得知其是否安全的，所以会尽心参数截断，跳转失效
7.网友发现的问题，未测试过
H5网页内嵌了一个iframe，iframe内的页面跳转时，整个页面也跟着跳转了，而按道理所有iframe内的操作都是单独的，不应该影响父页面。
想必微信的授权网页里有这个代码：

if(top !== self){
top.location.href = location.href;
} 
这是防止iframe引用的。

8.关于passive，为什么要设置passive:false

参考MDN一篇讲事件监听的文章 

https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener

9.关于svg的引用方式 及 获取

https://www.jianshu.com/p/1d4fefac4f55

https://www.cnblogs.com/boring09/p/4852972.html

10.设置为flex的元素，子元素为position:absolute时，在旧浏览器上会出现兼容问题，相关flex在该子元素的设置会失效。

11.文字或图片在行内被压得位置偏移，由于font-size和line-height的问题

12.window.onresize 在 window.onload阶段是无效的

13.swiper 单页 循环时，三页都是一模一样的，如果有操作，注意区别（以工作项目举例说明）

    $(".swiper-slide-duplicate").find("*[id], *[name], *[title]").removeAttr("id").removeAttr("name").removeAttr("title");
14.形如(function(){})()的立即执行函数，如果前面有表达式，没有分号，编辑器会解析为同一句表达式。

15.turn.min.js  'Touch' in window => 'ontouchstart' in window

16.turn.js 添加末尾封面需要注意单双页，有可能出现合不上的情况

17.element-ui el-scrollbar  (参考ele-publish项目：折叠面板内容可滚动 edit/EditContent - EditBg)


18. transform
(1)transform:scale(1.2),这种方式放大再缩小，会留下一条条白边
解决方法：scale3d 或者 translateZ(0)
(2)直接scale图片可能会更模糊
解决方法：使用matrix
(3)做电子书刊，实现拖动效果时，用matrix或者修改left/top，无论是添加在书本div的父元素上还是书本上，都会残留白线
解决方案：添加transform: translate3d(0px, 0px, 0px);或 translateZ(0)；在父元素，子元素用2d变换可去除白边
(4)以电子书刊为例，用transform 放大元素之后，如果用left,top来进行元素的移动，是需要除以scale的数值的，但是如果用transform进行平移，就不用考虑这个问题
(5)transform内部元素 fixed属性失效，会变为与absolute一致
19.浏览器全屏
function launchFullscreen(element) {
 if (element.requestFullscreen) {
  element.requestFullscreen()
 } else if (element.mozRequestFullScreen) {
  element.mozRequestFullScreen()
 } else if (element.msRequestFullscreen) {
  element.msRequestFullscreen()
 } else if (element.webkitRequestFullscreen) {
  element.webkitRequestFullScreen()
 }
}
 
launchFullscreen(document.documentElement) // 整个页面进入全屏
launchFullscreen(document.getElementById("id")) //某个元素进入全屏
function exitFullscreen() {
 if (document.exitFullscreen) {
  document.exitFullscreen()
 } else if (document.msExitFullscreen) {
  document.msExitFullscreen()
 } else if (document.mozCancelFullScreen) {
  document.mozCancelFullScreen()
 } else if (document.webkitExitFullscreen) {
  document.webkitExitFullscreen()
 }
}
exitFullscreen()
如360、QQ有双内核，可以用meta去指定，但是用户也可以切换内核，用户指定的话meta将会失效
强制Chromium内核，作用于360浏览器、QQ浏览器等国产双核浏览器：
<meta name="renderer" content="webkit"/>
强制Chromium内核，作用于其他双核浏览器：
<meta name="force-rendering" content="webkit"/>
如果有安装 Google Chrome Frame 插件则强制为Chromium内核，否则强制本机支持的最高版本IE内核，作用于IE浏览器：
<meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1"/>
19.隐藏滚动条
1）ios上需要事先预设好类名，安卓可以通过js动态添加类名 
yuansu::-webkit-scrollbar {
   display: none;
}
2）安卓端overflow:scroll无滚动条，添加相应样式可显示
.ex {
    background-color: aqua;
}
​
.no_scroll_bar::-webkit-scrollbar {
    display: none;
}
​
.ex::-webkit-scrollbar {
    -webkit-appearance: none;
}
​
.ex::-webkit-scrollbar:vertical {
    width: 12px;
}
​
.ex::-webkit-scrollbar:horizontal {
    height: 12px;
}
​
.ex::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, .5);
    border-radius: 10px;
    border: 2px solid #ffffff;
}
​
.ex::-webkit-scrollbar-track {
    border-radius: 10px;
    background-color: #ffffff;
}
20.获取宽高等
offsetWidth 或者 width等都无法获取到元素的小数位，都是整型
解决方法1 window.getComputedStyle(element)
function getStyle(element, attr) {         
    return window.getComputedStyle ? window.getComputedStyle(element, null)[attr] : element.currentStyle[attr];      
}
解决方法2 element.getBoundingClientRect()
21.el-scrollbar
  .el-scrollbar {
    .el-scrollbar__wrap {
      max-height:100%;
      //里层元素不要再设置高度
    }
  }
22. html draggable
以下拖拽时跟随鼠标的待拖拽区域，简称为拖拽区域
在chrome中，当可拖拽的元素为可滚动元素，并且拖拽区域有部分内容在可是区域外，如overflow:hidden，可能会将紧挨着的区域元素绘制进去
解决方案：调整拖拽区域层级（或整个可拖拽元素），但该区域盖过下层时，就可正确绘制到需要拖拽的区域

23. swiper内增加contenteditable元素不能编辑的问题

可以通过修改源码解决，这个是6.+版本，https://github.com/nolimits4web/swiper/pull/3949/commits/fcf1f8e407ae30a10672f5903281fab5ad0389fa

24. 解决body旋转90度后，swiper手势与页面移动方向相悖的问题
ontouchmove 方法内增加判断 
```js
//在使用的实例内直接赋值该属性即可
if (swiper.forcedVertical) {
    var diffY = touches.currentX - touches.startX;
    var diffX = touches.currentY - touches.startY;
} else {
    var diffX = touches.currentX - touches.startX;
    var diffY = touches.currentY - touches.startY;
}
```

25. window.DeviceMotionEvent
- 要https
- ios13+
```js
DeviceMotionEvent.requestPermission().then(function (state) {
    if ('granted' === state) {
       window.addEventListener('devicemotion', function () {
           // do something
       }, false);
    } else {
        alert('apply permission state: ' + state);
    }
}).catch(function(err){
    alert('error: ' + err);
```
- 报错 NotAllowedError: Requesting device orientation or motion access requires a user gesture to prompt
需要用户手动触发，如click,touchend,mouseup
- 开发者拒绝/同意授权后，想要再次弹出，需要app重新启动才会触发
- 可以在进入页面时，catch中捕获并弹出提示框（提示框可自定义），点击提示框再次弹出授权框
[JS - 获取手机陀螺仪](https://www.jianshu.com/p/9fe7a94af185)
[ios 13 陀螺仪DeviceOrientationEvent需要申请用户权限 js](https://www.cnblogs.com/iroading/archive/2004/01/13/12633173.html)
[在IOS中DeviceMotion及DeviceOrientation事件不触发的问题](https://blog.csdn.net/greenwishing/article/details/90258584)
[解决ios13+动作与方向的权限的获取问题，从而导致无法实现摇一摇](https://blog.csdn.net/chendawen250/article/details/105415897?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromBaidu-1.control)

26. [canvas 跨域和缓存问题] (https://blog.csdn.net/u012755393/article/details/85130135)
- 跨域 img.crossOrigin = 'Anonymous';
- 缓存 cdn服务器设置缓存，第二次请求图片时，浏览器不会再向服务器发送请求，再次发生跨域错误。

27. git push 问题 Unable to access ‘https://github.com/**/**/‘: OpenSSL SSL_read: Connection was aborted, errno 10053
解决方案： git config --global http.sslVerify false