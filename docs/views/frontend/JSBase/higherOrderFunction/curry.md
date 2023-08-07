
```js
//ES6
function curry(fn){
  const limit = fn.length //获取参数长度
  const _curry = (...curArgs) => {
    if (curArgs.length >= limit) {
      fn(...curArgs)
    }else{
      (...args) => _curry(...curArgs,...args)
    }
  }
  return _curry
}

//ES5
function curry(fn) {
    var limit = fn.length
    var slice = Array.prototype.slice
    return function _curry() {
        var preArgs = slice.call(arguments)
        if (preArgs.length >= limit) {
            return fn.apply(null, preArgs)
        } else {
            return function() {
                var curArgs = slice.call(arguments)
                return _curry.apply(null, preArgs.concat(curArgs))
            }
        }
    }
}

//增加占位符
// @param {String} [holder] 占位符
var curry = function(fn, holder) {
    var limit = fn.length
    return function judgeCurry(...preArgs) {
        if (preArgs.length >= limit && !preArgs.includes(holder)) {
            return fn.apply(null, preArgs)
        } else {
            return function(...curArgs) {
                while (curArgs.length > 0) {
                    var start = 0
                    var curValue = curArgs.shift()
                    while (preArgs[start] !== holder && start < preArgs.length) {
                        start++
                    }
                    preArgs[start] = curValue
                }
                return judgeCurry.apply(null, preArgs)
            }
        }
    }
}
```

```js
//参考文献2中提到的占位符
function curry(fn, length = fn.length, holder = curry) {
    return _curry.call(this, fn, length, holder, [], [])
}

function _curry(fn, length, holder, args, holders) {
    return function(..._args) {
        let params = args.slice();

        // let _holders = holders.slice();
        
        // 由于holders在闭包中并没有释放，如果声明一个预设好的被柯里化函数，想要重复使用，就会出现问题，如：
        // fn = function(a,b,c,d,e){console.log(a,b,c,d,e)}
        // _fn = curry(fn, 5, _)
        // baseFn = _fn(_, 2, 3, 4, 5)
        // baseFn(1)  1,2,3,4,5
        // baseFn(2)  f  (此时holders并非baseFn中的，而是baseFn(1)中的)

        // 可以使用深拷贝解决该问题
        let holderss = JSON.parse(JSON.stringify(holders))
        let _holders = holderss.slice();
        
        _args.forEach((arg, i) => {
            //真实参数 之前存在占位符 将占位符替换为真实参数
            if (arg !== holder && holderss.length) {
                let index = holderss.shift();
                _holders.splice(_holders.indexOf(index), 1);
                params[index] = arg;
            }
            //真实参数 之前不存在占位符 将参数追加到参数列表中
            else if (arg !== holder && !holderss.length) {
                params.push(arg);
            }
            //传入的是占位符,之前不存在占位符 记录占位符的位置
            else if (arg === holder && !holderss.length) {
                params.push(arg);
                _holders.push(params.length - 1);
            }
            //传入的是占位符,之前存在占位符 删除原占位符位置
            else if (arg === holder && holderss.length) {
                holderss.shift();
            }
        });
        // params 中前 length 条记录中不包含占位符，执行函数
        if (params.length >= length && params.slice(0, length).every(i => i !== holder)) {
            return fn.apply(this, params);
        } else {
            return _curry.call(this, fn, length, holder, params, _holders)
        }
    }
}
```

1. https://juejin.cn/post/6844903592822833160#heading-4
2. https://juejin.cn/post/6844903882208837645#heading-2