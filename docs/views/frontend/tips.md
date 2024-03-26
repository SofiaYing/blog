1. audio chrome 出于安全考虑 是不允许 audio 自动播放的，需要相应的配置
2. 微信 webview scroll bounce 解决办法

```js
var overscroll = function(el) {
  el.addEventListener("touchstart", function() {
    var top = el.scrollTop,
      totalScroll = el.scrollHeight,
      currentScroll = top + el.offsetHeight;
    //If we're at the top or the bottom of the containers
    //scroll, push up or down one pixel.
    //
    //this prevents the scroll from "passing through" to
    //the body.
    console.log("11", el, el.scrollHeight, el.scrollTop);
    if (top === 0) {
      el.scrollTop = 1;
    } else if (currentScroll === totalScroll) {
      el.scrollTop = top - 1;
    }
  });
  el.addEventListener("touchmove", function(evt) {
    //if the content is actually scrollable, i.e. the content is long enough
    //that scrolling can occur
    if (el.offsetHeight < el.scrollHeight) evt._isScroller = true;
    console.log("22", evt, el, el.offsetHeight);
  });
};
overscroll(document.querySelector("#divpar"));
document.body.addEventListener("touchmove", function(evt) {
  //In this case, the default behavior is scrolling the body, which
  //would result in an overflow.  Since we don't want that, we preventDefault.
  console.log("333");
  if (!evt._isScroller) {
    console.log("444");
    evt.preventDefault();
  }
});
```

3. 对象键值对为变量
   可：arr[index] = {[i]:j}
   可：arr[index][''+i+'']=j
   不可：arr[index]={i:j}     或    arr[index][i]=j

4.transform matrix=>
var transformStr = \$(self.el).css("transform");
var angle = 0;
​
if (transformStr !== "" && transformStr !== "none") {
var \_transformStr = transformStr.substring(7, transformStr.length - 1);
var transformArray = \_transformStr.split(',');
if (transformArray.length === 6) {
var a = transformArray[0];
var b = transformArray[1];
var c = transformArray[2];
var d = transformArray[3];
var scale = Math.sqrt(a _ a + b _ b);
var sin = b / scale;
var angle = Math.round(Math.atan2(b, a) \* (180 / Math.PI));
}
}
5.gif 重置可以通过清空 src，再赋值做到，注意同一时间反复读取同一个，状态可能不会更新 6.微信保护机制：比如微信浏览器内打开的网页内有 iframe，点击 iframe 嵌套的网页内的<a>再跳转的网页，微信是无法得知其是否安全的，所以会尽心参数截断，跳转失效 7.网友发现的问题，未测试过
H5 网页内嵌了一个 iframe，iframe 内的页面跳转时，整个页面也跟着跳转了，而按道理所有 iframe 内的操作都是单独的，不应该影响父页面。
想必微信的授权网页里有这个代码：

if(top !== self){
top.location.href = location.href;
}
这是防止 iframe 引用的。

8.关于 passive，为什么要设置 passive:false

参考 MDN 一篇讲事件监听的文章

https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener

9.关于 svg 的引用方式 及 获取

https://www.jianshu.com/p/1d4fefac4f55

https://www.cnblogs.com/boring09/p/4852972.html

10.设置为 flex 的元素，子元素为 position:absolute 时，在旧浏览器上会出现兼容问题，相关 flex 在该子元素的设置会失效。

11.文字或图片在行内被压得位置偏移，由于 font-size 和 line-height 的问题

12.window.onresize 在 window.onload 阶段是无效的

13.swiper 单页 循环时，三页都是一模一样的，如果有操作，注意区别（以工作项目举例说明）

\$(".swiper-slide-duplicate").find("_[id], _[name], \*[title]").removeAttr("id").removeAttr("name").removeAttr("title"); 14.形如(function(){})()的立即执行函数，如果前面有表达式，没有分号，编辑器会解析为同一句表达式。

15.turn.min.js  'Touch' in window => 'ontouchstart' in window

16.turn.js 添加末尾封面需要注意单双页，有可能出现合不上的情况

17.element-ui el-scrollbar  (参考 ele-publish 项目：折叠面板内容可滚动 edit/EditContent - EditBg)

18. transform
    (1)transform:scale(1.2),这种方式放大再缩小，会留下一条条白边
    解决方法：scale3d 或者 translateZ(0)
    (2)直接 scale 图片可能会更模糊
    解决方法：使用 matrix
    (3)做电子书刊，实现拖动效果时，用 matrix 或者修改 left/top，无论是添加在书本 div 的父元素上还是书本上，都会残留白线
    解决方案：添加 transform: translate3d(0px, 0px, 0px);或 translateZ(0)；在父元素，子元素用 2d 变换可去除白边
    (4)以电子书刊为例，用 transform 放大元素之后，如果用 left,top 来进行元素的移动，是需要除以 scale 的数值的，但是如果用 transform 进行平移，就不用考虑这个问题
    (5)transform 内部元素 fixed 属性失效，会变为与 absolute 一致 19.浏览器全屏
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
如 360、QQ 有双内核，可以用 meta 去指定，但是用户也可以切换内核，用户指定的话 meta 将会失效
强制 Chromium 内核，作用于 360 浏览器、QQ 浏览器等国产双核浏览器：

<meta name="renderer" content="webkit"/>
强制Chromium内核，作用于其他双核浏览器：
<meta name="force-rendering" content="webkit"/>
如果有安装 Google Chrome Frame 插件则强制为Chromium内核，否则强制本机支持的最高版本IE内核，作用于IE浏览器：
<meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1"/>
19. 隐藏滚动条
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

20. 获取宽高等
    offsetWidth 或者 width 等都无法获取到元素的小数位，都是整型
    解决方法 1 window.getComputedStyle(element)
    function getStyle(element, attr) {  
     return window.getComputedStyle ? window.getComputedStyle(element, null)[attr] : element.currentStyle[attr];  
    }
    解决方法 2 element.getBoundingClientRect()

21. html draggable
    以下拖拽时跟随鼠标的待拖拽区域，简称为拖拽区域
    在 chrome 中，当可拖拽的元素为可滚动元素，并且拖拽区域有部分内容在可是区域外，如 overflow:hidden，可能会将紧挨着的区域元素绘制进去
    解决方案：调整拖拽区域层级（或整个可拖拽元素），但该区域盖过下层时，就可正确绘制到需要拖拽的区域

22. swiper 内增加 contenteditable 元素不能编辑的问题
    !(参考图片)[./images/tips-swiper.png]
    可以通过修改源码解决，这个是 6.+版本，https://github.com/nolimits4web/swiper/pull/3949/commits/fcf1f8e407ae30a10672f5903281fab5ad0389fa

23. 解决 body 旋转 90 度后，swiper 手势与页面移动方向相悖的问题
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

- 要 https
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
  需要用户手动触发，如 click,touchend,mouseup
- 开发者拒绝/同意授权后，想要再次弹出，需要 app 重新启动才会触发
- 可以在进入页面时，catch 中捕获并弹出提示框（提示框可自定义），点击提示框再次弹出授权框
  [JS - 获取手机陀螺仪](https://www.jianshu.com/p/9fe7a94af185)
  [ios 13 陀螺仪 DeviceOrientationEvent 需要申请用户权限 js](https://www.cnblogs.com/iroading/archive/2004/01/13/12633173.html)
  [在 IOS 中 DeviceMotion 及 DeviceOrientation 事件不触发的问题](https://blog.csdn.net/greenwishing/article/details/90258584)
  [解决 ios13+动作与方向的权限的获取问题，从而导致无法实现摇一摇](https://blog.csdn.net/chendawen250/article/details/105415897?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromBaidu-1.control)

26. [canvas 跨域和缓存问题](https://blog.csdn.net/u012755393/article/details/85130135)

- 跨域 img.crossOrigin = 'Anonymous';
- 缓存 cdn 服务器设置缓存，第二次请求图片时，浏览器不会再向服务器发送请求，再次发生跨域错误。

27. git push 问题 Unable to access ‘https://github.com/**/**/‘: OpenSSL SSL_read: Connection was aborted, errno 10053
    解决方案： git config --global http.sslVerify false

28. 通过 js 改变 value，并不会触发 input 的 change,input 等事件

29. element-ui el-input 和 el-input textarea 字体样式不一样，是由于原生 textarea 加载字体的问题，可以在需要的地方显示重写 textarea 的 font-family 属性

30. 关闭浏览器之后发送请求问题

- 不稳定： 监听 unload 事件，用 img 的 src 发送请求。图片和后端建立链接，耗时了，所以不一定能到后端，浏览器不一定会等图片加载完毕然后再卸载文档
- 不稳定： Navigator.sendBeacon：
  [Navigator.sendBeacon()](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon)
  [Beacon API is broken](https://volument.com/blog/sendbeacon-is-broken#comments)
- ajax

31. 微信分享常见问题

- 设置缩略图、描述失败（无报错）：在微信内打开页面，页面未加载完成时，由于微信初始化设置未完成，分享时可能会出现设置缩略图、描述失败情况
- 部分手机在微信内无法打开 http 协议的地址

### 32. github push 失败

Failed to connect to github.com port 443 after 10 ms: Connection refused
设置代理
git config --global https.proxy http://127.0.0.1:1081
git config --global http.proxy http://127.0.0.1:1081
注意： 代理地址为本机代理服务器地址。（如使用了 vpn，要查看 vpn 的代理地址）
取消设置
git config --global --unset https.proxy
git config --global --unset http.proxy

### 33。本地代码远程 push 到 github

1. git init
   报错：git init xcrun: error: invalid active developer path

   - 安装 Xcode (Mac App Store)

2. git add .
3. git commit -m'information'
4. git remote add origin <url> (添加远程仓库地址（请将<url>替换为您的 GitHub 仓库的 URL）)
5. 添加远程仓库地址（请将<url>替换为您的 GitHub 仓库的 URL）
   报错：Support for password authentication was removed on August 13, 2021
   - 需要通过个人访问码的方式进行认证 https://blog.csdn.net/FatalFlower/article/details/119717823
   - 点击头像，选择 setting
   - developer settings
   - tokens(classic)
   - generate new token
   - 复选框没有特别的要求可以都选上，最后点击 generate token
   - 记得将 token 保存下来
6. git push --set-upstream origin master(这个命令是用于设置默认的上游分支，这样在后续的 git push 或 git pull 操作中就不需要每次都指定远程分支了。)
