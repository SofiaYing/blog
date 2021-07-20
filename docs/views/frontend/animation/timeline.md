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
  animations.startTime = startTime  // 我认为可以描述为插入到时间轴的位置，timelineOffset
}
```
### delay

```js
Timeline.prototype.start = function() {
    this.startTime = Date.now()

    this.tick = (t) => {
        let now = Date.now()
        for (let i = 0; i < this.animations.length; i++) {
            let curAnime = this.animations[i]

            // 播放经过这段时间相当于消耗掉的，不应影响时间轴
            let passTime
            if (curAnime.startTime < this.startTime){
              //以时间轴开始时间为准
              passTime = now - this.startTime - curAnime.delay
            }else{
              //以动画加入时间为准
              passTime = now - curAnime.startTime - curAnime.delay
            }

            if (passTime > curAnime.during) {
                this.animations.splice(i, 1)
                currentTime = curAnime.during
            }
            //
            if (passTime >= 0) {
                curAnime.run(passTime)
            }
        }

        requestAnimationFrame(this.tick)
    }

    this.tick()
}
```
### pause
```js
Timeline.prototype.start = function() {
    this.startTime = Date.now()
    this.tick = () => {
        ...
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
function Timeline() {
    this.animations = []
    this.pauseDuring = 0  //暂停经过的时间
    this.lastTime = 0  // 暂停时的时间点（上一次播放结束的时间）
}
Timeline.prototype.start = function() {
    this.startTime = Date.now()

    this.tick = (t) => {
        let now = Date.now()
        for (let i = 0; i < this.animations.length; i++) {
            let curAnime = this.animations[i]

            // 播放经过这段时间相当于消耗掉的，不应影响时间轴
            let passTime
            if (this.startTime > curAnime.startTime) {
                passTime = now - this.startTime - curAnime.delay - this.pauseDuring
            } else {
                passTime = now - curAnime.startTime - curAnime.delay - this.pauseDuring
            }

            if (passTime > curAnime.during) {
                this.animations.splice(i, 1)
                currentTime = curAnime.during
            }


            // 有可能出现时间还没到该播放动画的时候，比如轴播放到第8s，第9s才开始播放某动画，8-9=-1，要对该情况进行判断
            if (passTime >= 0) {
                curAnime.run(passTime)
            }

        }

        this.tickHandler = requestAnimationFrame(this.tick)
    }

    this.tick()
}

Timeline.prototype.pause = function() {
    this.lastTime = Date.now()
    cancelAnimationFrame(this.tickHandler)
}

Timeline.prototype.resume = function() {
    let now = Date.now()
    this.pauseDuring += (now - this.lastTime)
    this.tick()
}
```
## anime.js 核心
```js
// Core
// 算法 + 时间 = 位置

// currentTime 当前位置所对应的时间，当于记录了动画播放到了什么位置
// engineTime 当前位置消耗时间
// lastTime 上一次计算结束后，位置对应时间
// startTime 上一次调用raf的时间
// insTime 当前位置所消耗时间（adjustTime匹配反转状态），根据engineTime计算
// insDuring 动画持续时间
// delay 延迟
// timelineOffset 从什么时间点开始动画

// 0 开始，播放到了5
// 按了pause，lastTime = 5 
// 8的时候play，startTime=0,startTime=now(8)
// 9的时候，engineTime = 9 - 8 - 5

// timeline也看成一个anime，只是多了一个不定的开始时间，用timelineOffset

let activeInstances = [];
let pausedInstances = [];
let raf;

// 关键函数
const engine = (() => {
    function play() {
        raf = requestAnimationFrame(step);
    }

    // t 是resquestAnimatinFrame给callback回传的一个参数，精确到毫秒级
    // 表示requestAnimationFrame() 开始去执行回调函数的时刻,与performance.now()的返回值相同
    // performance 的 now() 方法来获取当前的时间戳的值（自创建上下文以来经过的时间）,以一个恒定的速率慢慢增加的，它不会受到系统时间的影响
    // performance.timing.navigationStart + performance.now() 约等于 Date.now()
    function step(t) {
        let activeInstancesLength = activeInstances.length;
        if (activeInstancesLength) {
            let i = 0;
            while (i < activeInstancesLength) {
                const activeInstance = activeInstances[i];
                if (!activeInstance.paused) {
                    activeInstance.tick(t);
                } else {
                    const instanceIndex = activeInstances.indexOf(activeInstance);
                    if (instanceIndex > -1) {
                        activeInstances.splice(instanceIndex, 1);
                        activeInstancesLength = activeInstances.length;
                    }
                }
                i++;
            }
            play();
        } else {
            // 因为是针对单独的一个instance，在动画播完后就可以取消时间计算了。
            // 此时raf === undefined , 再次play的时候，会重新启动tick
            raf = cancelAnimationFrame(raf);
        }
    }
    return play;
})();


// Public Instance

function anime(params = {}) {

    let startTime = 0,
        lastTime = 0,
        now = 0;
    let children, childrenLength = 0;
    let resolve = null;

    function makePromise(instance) {
        const promise = window.Promise && new Promise(_resolve => resolve = _resolve);
        instance.finished = promise;
        return promise;
    }

    let instance = createNewInstance(params);
    let promise = makePromise(instance);

    function toggleInstanceDirection() {
        const direction = instance.direction;
        if (direction !== 'alternate') {
            instance.direction = direction !== 'normal' ? 'normal' : 'reverse';
        }
        instance.reversed = !instance.reversed;
        children.forEach(child => child.reversed = instance.reversed);
    }

    //将时间与动画方向挂钩
    function adjustTime(time) {
        return instance.reversed ? instance.duration - time : time;
    }

    function resetTime() {
        startTime = 0;
        lastTime = adjustTime(instance.currentTime) * (1 / anime.speed);
    }

    function seekChild(time, child) {
        if (child) child.seek(time - child.timelineOffset);
    }

    function syncInstanceChildren(time) {
        if (!instance.reversePlayback) {
            for (let i = 0; i < childrenLength; i++) seekChild(time, children[i]);
        } else {
            for (let i = childrenLength; i--;) seekChild(time, children[i]);
        }
    }

    // 关键函数
    // 计算动画当前位置，并且赋值
    function setAnimationsProgress(insTime) {
        let i = 0;
        const animations = instance.animations;
        const animationsLength = animations.length;
        while (i < animationsLength) {
            const anim = animations[i];
            const animatable = anim.animatable;
            const tweens = anim.tweens;
            const tweenLength = tweens.length - 1;
            let tween = tweens[tweenLength];
            // Only check for keyframes if there is more than one tween
            if (tweenLength) tween = filterArray(tweens, t => (insTime < t.end))[0] || tween;
            const elapsed = minMax(insTime - tween.start - tween.delay, 0, tween.duration) / tween.duration;
            const eased = isNaN(elapsed) ? 1 : tween.easing(elapsed);
            const strings = tween.to.strings;
            const round = tween.round;
            const numbers = [];
            const toNumbersLength = tween.to.numbers.length;
            let progress;
            for (let n = 0; n < toNumbersLength; n++) {
                let value;
                const toNumber = tween.to.numbers[n];
                const fromNumber = tween.from.numbers[n] || 0;
                if (!tween.isPath) {
                    value = fromNumber + (eased * (toNumber - fromNumber));
                } else {
                    value = getPathProgress(tween.value, eased * toNumber);
                }
                if (round) {
                    if (!(tween.isColor && n > 2)) {
                        value = Math.round(value * round) / round;
                    }
                }
                numbers.push(value);
            }
            // Manual Array.reduce for better performances
            const stringsLength = strings.length;
            if (!stringsLength) {
                progress = numbers[0];
            } else {
                progress = strings[0];
                for (let s = 0; s < stringsLength; s++) {
                    const a = strings[s];
                    const b = strings[s + 1];
                    const n = numbers[s];
                    if (!isNaN(n)) {
                        if (!b) {
                            progress += n + ' ';
                        } else {
                            progress += n + b;
                        }
                    }
                }
            }
            setProgressValue[anim.type](animatable.target, anim.property, progress, animatable.transforms);
            anim.currentValue = progress;
            i++;
        }
    }

    function setCallback(cb) {
        if (instance[cb] && !instance.passThrough) instance[cb](instance);
    }

    function countIteration() {
        if (instance.remaining && instance.remaining !== true) {
            instance.remaining--;
        }
    }

    // 关键函数
    // 对当前engineTime进行判断，确定动画方案
    // 接受一个消耗的时间值，在内部对其进行适配和定义了各种情况的动画起始点，传递给setAnimationsProgress
    function setInstanceProgress(engineTime) {
        const insDuration = instance.duration; //动画持续时间
        const insDelay = instance.delay;
        const insEndDelay = insDuration - instance.endDelay; //实际持续时间，endDelay 是last tween的delay
        const insTime = adjustTime(engineTime);
        instance.progress = minMax((insTime / insDuration) * 100, 0, 100); //计算位置
        instance.reversePlayback = insTime < instance.currentTime;
        if (children) { syncInstanceChildren(insTime); }
        if (!instance.began && instance.currentTime > 0) {
            instance.began = true;
            setCallback('begin');
        }
        if (!instance.loopBegan && instance.currentTime > 0) {
            instance.loopBegan = true;
            setCallback('loopBegin');
        }

        // 消耗的时间小于应该开始的时间 并且 当前位置不在起点
        if (insTime <= insDelay && instance.currentTime !== 0) {
            // 从头开始
            setAnimationsProgress(0);
        }

        // 消耗的时间超出了持续时间 并且当前位置不在终点  或者 未设定持续时间
        if ((insTime >= insEndDelay && instance.currentTime !== insDuration) || !insDuration) {
            // 从结束点开始
            setAnimationsProgress(insDuration);
        }
        // 消耗的时间大于应该开始的时间 并且 消耗的时间在持续时间范围内
        if (insTime > insDelay && insTime < insEndDelay) {
            if (!instance.changeBegan) {
                instance.changeBegan = true;
                instance.changeCompleted = false;
                setCallback('changeBegin');
            }
            setCallback('change');
            setAnimationsProgress(insTime);
        } else {
            if (instance.changeBegan) {
                instance.changeCompleted = true;
                instance.changeBegan = false;
                setCallback('changeComplete');
            }
        }

        instance.currentTime = minMax(insTime, 0, insDuration);
        if (instance.began) setCallback('update');
        if (engineTime >= insDuration) {
            lastTime = 0;
            countIteration();
            if (!instance.remaining) {
                instance.paused = true;
                if (!instance.completed) {
                    instance.completed = true;
                    setCallback('loopComplete');
                    setCallback('complete');
                    if (!instance.passThrough && 'Promise' in window) {
                        resolve();
                        promise = makePromise(instance);
                    }
                }
            } else {
                startTime = now;
                setCallback('loopComplete');
                instance.loopBegan = false;
                if (instance.direction === 'alternate') {
                    toggleInstanceDirection();
                }
            }
        }
    }

    // 关键函数
    // 配置startTime上一次调用raf的时间 和 engineTime当前位置消耗时间
    // 通过t，计算出距离上一次调用实际消耗的时间engineTime
    instance.tick = function(t) {
        now = t;
        // startTime 如果首次执行 就是now，否则就是上一次tick的时间
        if (!startTime) startTime = now;
        //lastTime 上一次执行后动画对应的位置时间戳 
        //engineTime 到动画目前为止消耗的总时间，一般理论上讲是lastTime+16.6667:(lastTime + now - startTime) * anime.speed
        //anime.speed 目前看就是1，没什么作用
        setInstanceProgress((now + (lastTime - startTime)) * anime.speed);
    }

    instance.seek = function(time) {
        setInstanceProgress(adjustTime(time));
    }

    instance.pause = function() {
        instance.paused = true;
        resetTime();
    }

    instance.play = function() {
        if (!instance.paused) return;
        if (instance.completed) instance.reset();
        instance.paused = false;
        activeInstances.push(instance);
        resetTime();
        // startTime = 0;
        // lastTime = adjustTime(instance.currentTime) * (1 / anime.speed);
        if (!raf) engine();
    }

    instance.reverse = function() {
        toggleInstanceDirection();
        resetTime();
    }

    instance.restart = function() {
        instance.reset();
        instance.play();
    }

    instance.reset();

    if (instance.autoplay) instance.play();

    return instance;

}

// Timeline

function timeline(params = {}) {
    let tl = anime(params);
    tl.duration = 0;
    tl.add = function(instanceParams, timelineOffset) {
        const tlIndex = activeInstances.indexOf(tl);
        const children = tl.children;
        if (tlIndex > -1) activeInstances.splice(tlIndex, 1);

        function passThrough(ins) { ins.passThrough = true; };
        for (let i = 0; i < children.length; i++) passThrough(children[i]);
        let insParams = mergeObjects(instanceParams, replaceObjectProps(defaultTweenSettings, params));
        insParams.targets = insParams.targets || params.targets;
        const tlDuration = tl.duration;
        insParams.autoplay = false;
        insParams.direction = tl.direction;
        insParams.timelineOffset = is.und(timelineOffset) ? tlDuration : getRelativeValue(timelineOffset, tlDuration);
        passThrough(tl);
        tl.seek(insParams.timelineOffset);
        const ins = anime(insParams);
        passThrough(ins);
        const totalDuration = ins.duration + insParams.timelineOffset;
        children.push(ins);
        const timings = getInstanceTimings(children, params);
        tl.delay = timings.delay;
        tl.endDelay = timings.endDelay;
        tl.duration = timings.duration;
        tl.seek(0);
        tl.reset();
        if (tl.autoplay) tl.play();
        return tl;
    }
    return tl;
}
```


# 苹果官网的动画是如何实现的
# bilibili官网的banner是如何实现的
[bilibili Autumn Banner](https://codepen.io/stevenlei/pen/MWeLEyx)
[bilibili Wintter Banner](https://codepen.io/stevenlei/pen/VwKQwNj)
[Bilibili网站首图动态Banner开发实现](https://juejin.cn/post/6944898818164916237)
[如何使用 Vue 3 和 Canvas 实现哔哩哔哩首页 banner?](https://juejin.cn/post/6884922737077256200)
[哔哩哔哩春季动态banner是怎么实现的？](https://juejin.cn/post/6951319753717710879#comment)


参考文献
1. [用 JavaScript 实现时间轴与动画 - 前端组件化](https://juejin.cn/post/6947519943332069390)
2. [[源码阅读]解析Anime(JS动画库)核心(1)](https://segmentfault.com/a/1190000016384897)
3. [Anime.js动画库](https://www.animejs.cn/)
4. [聊聊苹果营销页中几个有趣的交互动画](https://juejin.cn/post/6844904168281358349)
5. [用65行代码实现JavaScript动画序列播放](https://juejin.cn/post/6943433312371212302)
6. [关于动画，你需要知道的](https://h5jun.com/post/animations-you-should-know.html)
7. [合成层](https://fed.taobao.org/blog/taofed/do71ct/performance-composite/) 
8. [Tips for Writing Animation Code Efficiently](https://css-tricks.com/tips-for-writing-animation-code-efficiently/)
9. [CSS技巧：逐帧动画抖动解决方案](https://jelly.jd.com/article/6006b1045b6c6a01506c87f0)