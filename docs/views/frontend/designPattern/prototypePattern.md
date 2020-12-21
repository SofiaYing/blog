---
title: 原型模式
date: 2020-12-21
categories:
  - frond-end
tags :
  - design patterns
---
原型模式不仅是一种设计模式，它还是一种编程范式（programming paradigm），是 JavaScript 面向对象系统实现的根基。
借助Prototype来实现对象的创建和原型的继承，那么我们就是在应用原型模式。

java中原型模式的出现是为了实现类型之间的解耦？理解是new时需要注意传入数据类型，但是copy不用考虑这种问题
## 原型链的作用
```js
//无原型链，每个实例的共有属性都会消耗一份内存
var dogs = []
var dog
for(var i=0;i<100;i++){
    dog = {
        sound: '汪汪汪',
        species: 'dog',
        jump: function(){},
    }
    dogs.push(dog)
}
​
//原型链
var dogsCommon = {  
    species: 'dog',
    jump: function(){}
}
var dogs = []
var dog
for(var i=0;i<100;i++){
    dog = {
        hair = "black",
    }
    dog.__proto__ = dogsCommon
    dogs.push(dog)
}


//构造函数
var dogsCommon = {   
    species: 'dog',
    jump: function(){}
}
function creatDog(){
    var obj = {
        hair = "black",
    }
    obj.__proto__ = dogsCommon
    return obj
}
var dogs = []
for(var i=0;i<100;i++){
    dogs.push(creatDog())
}
```

### 改进
```js
//createDog.dogsCommon = { 
createDog.prototype = {  //共有属性有个名字叫原型prototype
    //从名字上建立dogsCommon与createDog的关系
    //函数createDog是对象！
    //createDog函数声明提升，可以对createDog进行操作
    species: 'dog',
    jump: function(){}
}
function createDog(){  
    var obj = {
        hair = "black",
    }
    // obj.__proto__ = createDog.dogsCommon 
    obj.__proto__ = createDog.prototype //内容在执行时才会生效，即使createDog写在前面，也能调用createDog.prototype
    return obj
}
var dogs = []
for(var i=0;i<100;i++){
    //dogs.push(createDog())
    Array.prototype.push.call(
        dogs,createDog.call()
    )
}
```
## new
满足批量创建对象的需求
new 帮忙将上面的代码帮忙写好
(1)js已经帮忙初始化一个空对象this，不需要自己去想变量名
(2)帮忙创建好obj. __proto__ = createDog.prototype, 同时会为其自动创建constructor属性，表述了所有的实例都是由createDog创建的。
(3)已经帮忙返回this，不需要自己去写。如果自己写了return,如return {}，最后就会返回一个{}，this没有返回，如果return 1,js认为构造函数应该返回对象，所以会忽略这句话
注意：不要对createDog.prototype重新赋值，会使得prototype指向另一个没有constructor的对象
```js
function Dog(hair){  
    // var obj = {
    //    hair = "black",
    // }
    // obj.__proto__ = createDog.prototype 
    // return obj
    this.hair = hair || 'black'
}
//createDog.prototype = {  
//    constructor: createDog  
      // js默认设置该属性
      // 不要对createDog.prototype重新赋值，会使得prototype指向另一个没有constructor的对象
//}
Dog.prototype.species = 'dog'
Dog.prototype.jump = function(){}
```

约定俗成的一些事：
1. 构造函数首字母大写 CreatSoldier
2. 构造函数省略creat 因为new已经代表了creat的意思
3. 如果构造函数没有参数，可以省略括号
```js
//模拟实现new操作符
function myNew(constructor){
    //ES6 new.target 返回new命令作用于的那个构造函数。
    //如果构造函数不是通过new命令或Reflect.construct()调用的，new.target会返回undefined
    //因此这个属性可以用来确定构造函数是怎么调用的。
    myNew.target = constructor
    
    let newObject = Object.create(constructor.prototype)
    
    //判断构造函数返回值
    //var argsArr = [...args]   //myNew(constructor,...args)
    //var argsArr = Array.from(arguments).slice(1)
    //var argsArr = [].slice.call(arguments,1)
    let argsArr = Array.prototype.slice.call(arguments,1)   
    let constructorReturn = constructor.apply(newObject,argsArr)  //绑定this
    if((typeof constructorReturn === 'object' && constructorReturn!==null) 
       || typeof constructorReturn==='function'){
        return constructorReturn
    }
    
    //如果构造函数没有返回对象类型`Object`(包含`Functoin`, `Array`, `Date`, `RegExg`, `Error`)，那么`new`表达式中的函数调用会自动返回这个新的对象。
    return newObject  
}
```

## 继承
```js
//JS模拟继承
function Dogs(options){
    this.species = options.species
    this.sound = options.sound
}
Dogs.prototype.jump = function(){}
Dogs.prototype.eat = function(){}
​
//继承
function Corgi(options){
    //特有属性继承
    Dogs.call(this,options)
    this.leg = "short"
}
Corgi.prototype.wagTail = function(){}
​
//公有属性继承
Corgi.prototype.__proto__ = Dogs.prototype
​
//实例化
var laifu = new Corgi({species'Corgi',sound:'汪汪汪'})
```
### 改进
__proto__不推荐使用，但是可以用new,
即不要这样：Corgi.prototype.__proto__ = Dogs.prototype
```js
//new做的事
function ins(){
    this = {}
    this.__proto__ = father.prototype
    return this
}
//继续改写
function Dogs(){
    this = {} //这个空对象最后只有一个属性__proto__
    this.__proto__ = father.prototype
    // Corgi.prototype.__proto__ = Dogs.prototype
    return this
}
​
Corgi.prototype = new Dogs()
//推断
Corgi.prototype.__proto__ === this.__proto__ === Dogs.prototype
​
//但是此时有两个问题，1.Dogs已经定义；2.Dogs内部有this
//模拟空函数,保留Dogs的共有属性，不保留特有属性
function fakeDogs(){}
fakeDogs.prototype = Dogs.prototype
Corgi.prototype = new fakeDogs()
```
### 整理
```js
function Dogs(options){
    this.species = options.species;
    this.sound = options.sound;
}
Dogs.prototype.eat = function(){}
Dogs.prototype.jump = function(){}
​
//继承
function Corgi(options){
    //特有属性继承
    Dogs.call(this,options)
    //Corgi特有属性
    this.character = options.character
}
​
//相当于 Corgi.prototype.__proto__ = Dogs.prototype
//公有属性继承(三行代码)，写在自己的公有属性前面，避免覆盖
​
//ES3 兼容IE
function fakeDogs(){}
fakeDogs.prototype = Dogs.prototype
Corgi.prototype = new fakeDogs()
//ES5 不兼容IE
Corgi.prototype = Object.create(Dogs.prototype)
​
//Corgi公有属性
Corgi.prototype.wagTail = function(){}
Corgi.prototype.legs = 'short'
​
var laifu = new Corgi({species'Corgi',sound:'汪汪汪',character:'活泼'})
```
## ES6：Class
```js
class Dogs{
    //构造函数写在constructor里
    constructor(options){
        this.species = options.species
        this.hair = options.hair
    }
    eat(){}
    jump(){}
}
// extends  相当于 Corgi.prototpye = Object.create(Dogs.prototype)
// 但是公有属性不支持非函数的了
class Corgi extends Dogs{
    constructor(options){
        //有父类去构造，继承特有属性，写在自己的特有属性之前，子元素有构造函数的话必须写
        super(options)
        this.legs = "short"
        this.character = options.character
    }
    wagTail(){}
}
```

java中的面向对象
class/abstract/interface
java中的类设计更完整
private/default/protect/public + 类型(如Long/Int等)
javascript中要实现这些概念需要去模仿
```js
function Create(){
  privateFunction()
}
Create.prototype.protectFunction = function(){}

class Create{
  static privateFunction()
  protectFunction(){}
}
```
java中的Abstract和Interface（类似于factoryPattern.md中栗子中的抽象类）：
共同点
A.两者都是抽象类，都不能实例化
B.Interface实现类和abstract继承类都必须实现抽象方法
不同点
A.Interface需要实现,用implements;Abstract 需要继承,用exends
B.一个类可以实现多个Interface ;一个类只能继承一个Abstract
C.Interface强调功能的实现；Abstract强调从属关系
D.Interface的所有抽象类都只有声明没有方法体；Abstract抽象方法可以选择实现，也可以选择继续声明为抽象方法，无需实现，留给子类去实现
interface的应用场合
A. 类与类之间需要特定的接口进行协调，而不在乎其如何实现。

java中使用原型模式，举个栗子：
连接数据库时，初始化声明可能很复杂（如用户，密码，数据库地址等等以及自己的方法）,
如果每次都要重复这个过程就很复杂，就可以事先做好一个模板，再用的时候直接拿到一个副本即可，这里面还可以设计回收池的问题，如一开始先声明很多个模板，然后自己的方法那为空，使用的时候将它们的副本放到池子里，就可以在池子里拿出来用，用过之后再释放资源。









参考文献：
1.[能否模拟实现JS的new操作符](https://juejin.im/post/5bde7c926fb9a049f66b8b52)