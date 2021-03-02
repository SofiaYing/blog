babel 转换单独的js文件（非工程）
babel6
安装babel
```js
//首先全局安装Babel。
npm install -g babel-cli
//或
npm install -g babel-cli --save-dev
//在执行安装到项目中命令之前，要先在项目根目录下新建一个package.json文件。

//或
//将babel直接安装到项目中
//同时它会自动在package.json文件中的devDependencies中加入babel-cli
{
  "devDependencies": {
    "babel-cli": "^6.22.2"
}
```
配置babel
创建.babelrc文件
```js
{
  "presets": [],
  "plugins": []
}
```
ES2015转换
npm install --save-dev babel-preset-es2015
```js
//.babelrc
{
  "presets": [
    "es2015",
  ],
  "plugins": []
}
//package.json
//babel src --out-dir lib  
//src是需要转换的文件夹名称 lib是放置转换后的文件名称
"scripts": {
    "babel": "babel src --out-dir lib",
    "test": "echo \"Error: no test specified\" && exit 1"
},
```

babel 7 
@babel/preset-env
首先，Babel 推荐使用 @babel/preset-env 套件来处理转译需求。顾名思义，preset 即“预制套件”，包含了各种可能用到的转译工具。之前的以年份为准的 preset 已经废弃了，现在统一用这个总包。
同时，babel 已经放弃开发 stage-* 包，以后的转译组件都只会放进 preset-env 包里。
(最近折腾 @babel/preset-env 的一些小心得)[https://blog.meathill.com/js/some-tips-of-babel-preset-env-config.html]
(Babel7 知识梳理)[https://www.cnblogs.com/cczlovexw/p/11988008.html]

安装core-js 
npm install core-js@3 --save
```js
//.babelrc.json
{
    "presets": [
        ["@babel/preset-env",
            {
                "useBuiltIns": "entry",
                "corejs": 3
            }
        ]
    ]
}
```

补充：
babel.config.json vs .babelrc.json
您要编译node_modules吗？
babel.config.json 是给你的！

您的配置仅适用于项目的单个部分吗？
.babelrc.json 是给你的


useBuiltIns 
数据类型: "usage" | "entry" | false, 默认false
这个选项配置了@babel/preset-env如何处理polyfill
当usage或者entry选项被使用, @babel/preset-env将直接饮用cores-js模块相当于暴露imports或者requries, 这一位置core-js将被直接解析到文件本身而且需要可访问
因为@babel/polyfill在Babel 7.4.0已被废弃, 推荐直接添加core-js和通过corejs选项设置版本
```js
npm install core-js@3 --save
// or
npm install core-js@2 --save
```
"useBuiltIns":"usage"
在文件需要的位置单独按需引入，可以保证在每个bundler中只引入一份
"useBuiltIns":"entry"
在项目入口引入一次（多次引入会报错）
插件@babel/preset-env会将把@babel/polyfill根据实际需求打散，只留下必须的