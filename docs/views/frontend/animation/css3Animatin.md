### transition

属于补间动画，需要提供起始和结束两个关键帧，浏览器才能够完成样式差异比对并计算出对应的过渡动画。所以它有两个特点：

1. 由于首次渲染元素的样式只会有一个关键帧，浏览器无法进行样式差异比对，所以在首屏渲染时 transition 一般不会生效
2. 由于浏览器是根据样式差异化的两帧自动计算并过渡，所以 transition 只支持可识别中间值的属性 (如大小、颜色、位置、透明度等)，而如 display 属性则不支持。

transition 属性是一个简写属性，用于设置四个过渡属性
默认 all 0 ease 0

- transition-property 规定设置过渡效果的 CSS 属性的名称。
- transition-duration 规定完成过渡效果需要多少秒或毫秒。
- transition-timing-function 规定速度效果的速度曲线。
- transition-delay 定义过渡效果何时开始。

```css
/* 单个属性 */
div {
  width: 100px;
  height: 100px;
  background: blue;
  transition: width 2s;
  -moz-transition: width 2s; /* Firefox 4 */
  -webkit-transition: width 2s; /* Safari and Chrome */
  -o-transition: width 2s; /* Opera */
}
div:hover {
  width: 300px;
}

/* 全部属性 */
div {
  width: 100px;
  height: 100px;
  background: blue;
  transition: all 2s;
}
div:hover {
  width: 10px;
  height: 10px;
}
```

### animation

- animation-name 规定需要绑定到选择器的 keyframe 名称。。
- animation-duration 规定完成动画所花费的时间，以秒或毫秒计。
- animation-timing-function 规定动画的速度曲线。
- animation-delay 规定在动画开始之前的延迟。
- animation-iteration-count 规定动画应该播放的次数。
- animation-direction 规定是否应该轮流反向播放动画。
  注: 请始终规定 animation-duration 属性，否则时长为 0，就不会播放动画了。

animation-timing-function

- linear 动画从头到尾的速度是相同的。
- ease 默认。动画以低速开始，然后加快，在结束前变慢。
- ease-in 动画以低速开始。
- ease-out 动画以低速结束。
- ease-in-out 动画以低速开始和结束。

animation-iteration-count

- infinite 无数次

animation-direction

- normal 默认值。动画应该正常播放。
- alternal 动画应该轮流反向播放。

```css
div {
  position: absolute;
  top: 0;
  left: 100px;
  width: 300px;
  height: 300px;
  animation: move 10s linear infinite alternate;
  /* 10s完成 速度一致 无数次 反向播放 */
}
@keyframes move {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(900px);
  }
  0% {
    background-color: yellow;
  }
  50% {
    background-color: green;
  }
  100% {
    background-color: orange;
  }
}
```
