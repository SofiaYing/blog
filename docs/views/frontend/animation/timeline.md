# 如何做一个时间轴动画
动画的原理看似复杂，其实就是每帧不停得渲染,那么怎么渲染每一帧呢？

缓动函数：通常是描述给定完整性百分比的属性的值的函数。我的理解是可以计算状态的函数，
通过缓动函数去做：
通过**初始位置**,**结束位置**,**持续时间**,**已消耗的时间**就可以计算出当前所在位置。
初始位置、结束位置和持续时间是作为参数传入配置的，因此计算已消耗时间就是完成动画的核心。
动画都是算法+时间=位置这么算出来的。

### 基础时间轴的实现
```js
function Timeline() {
  this.animations = []
}

Timeline.prototype.start = function() {
    this.startTime = Date.now()
    this.tick = () => {
        let passTime = Date.now() - this.startTime

        for (let i = 0; i < this.animations.length; i++) {
            let curAnime = this.animations[i]
            if (passTime > curAnime.during) {
                this.animations.splice(i, 1)

                //最后需要让动画运行一次最终状态
                currentTime = curAnime.during
            }
            curAnime.run(passTime)
        }
        requestAnimationFrame(this.tick)
    }
    
    this.tick()
}

Timeline.prototype.add = function(animation) {
    this.animations.push(animation)
}

function Animation(options) {
    this.startValue = options.startValue
    this.endValue = options.endValue
    this.during = options.during
    this.object = options.object
    this.property = options.property
    this.delay = options.delay
}

Animation.prototype.run = function(passTime) {
    // 变化区间= 结束 - 初始
    // 变化值 = 变化区间 * （消耗时间/持续时间）
    let currentValue = (passTime / during) * (endValue - startValue)

    // this.object.style[this.property] = currentValue + 'px'
}
```
### 在时间轴任意时间可加入动画
```js
Timeline.prototype.start = function() {
    this.startTime = Date.now()
    this.tick = () => {
        let now = Date.now()

        for (let i = 0; i < this.animations.length; i++) {
            let curAnime = this.animations[i]

            //新增逻辑
            let passTime 
            if (curAnime.startTime < this.startTime){
              //以时间轴开始时间为准
              passTime = now - this.startTime
            }else{
              //以动画加入时间为准
              passTime = now - curAnime.startTime
            }

            if (passTime > curAnime.during) {
                this.animations.splice(i, 1)

                currentTime = curAnime.during
            }
            curAnime.run(passTime)
        }
        requestAnimationFrame(this.tick)
    }
    
    this.tick()
}

Timeline.prototype.add = fucntion(animation,startTime){
  // 动画加入时间轴的时间（动画时间开始播放的时间）
  if (arguments.length < 2) {
    startTime = Date.now()
  }
  animations.startTime = startTime
}
```
### delay

```js
let passTime 
if (curAnime.startTime < this.startTime){
  //以时间轴开始时间为准
  passTime = now - this.startTime - curAnime.delay
}else{
  //以动画加入时间为准
  passTime = now - curAnime.startTime - curAnime.delay
}
```
### pause
```js
Timeline.prototype.start = function() {
    this.startTime = Date.now()
    this.tick = () => {
        let now = Date.now()

        for (let i = 0; i < this.animations.length; i++) {
            let curAnime = this.animations[i]

            let passTime 
            if (curAnime.startTime < this.startTime){
              passTime = now - this.startTime
            }else{
              passTime = now - curAnime.startTime
            }

            if (passTime > curAnime.during) {
                this.animations.splice(i, 1)

                currentTime = curAnime.during
            }
            curAnime.run(passTime)
        }
        // 将计时器存储下来，以便于停止
        this.tickHandler = requestAnimationFrame(this.tick)
    }
    
    this.tick()
}

Timeline.prototype.pause = function() {
    cancelAnimationFrame(this.tickHandler)
}
```
### resume
```js

```








# 阅读anime.js的一些启示
[[源码阅读]解析Anime(JS动画库)核心(1)](https://segmentfault.com/a/1190000016384897)
# 苹果官网的动画是如何实现的
# bilibili官网的banner是如何实现的
[bilibili Autumn Banner](https://codepen.io/stevenlei/pen/MWeLEyx)
[bilibili Wintter Banner](https://codepen.io/stevenlei/pen/VwKQwNj)


参考文献
1. [用 JavaScript 实现时间轴与动画 - 前端组件化](https://juejin.cn/post/6947519943332069390)
2. [聊聊苹果营销页中几个有趣的交互动画](https://juejin.cn/post/6844904168281358349)
3. [Anime.js动画库](https://www.animejs.cn/)
4. [用65行代码实现JavaScript动画序列播放](https://juejin.cn/post/6943433312371212302)
5. [关于动画，你需要知道的](https://h5jun.com/post/animations-you-should-know.html)
6. [合成层](https://fed.taobao.org/blog/taofed/do71ct/performance-composite/) 