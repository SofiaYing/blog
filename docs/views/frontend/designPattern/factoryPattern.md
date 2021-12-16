---
title: 工厂模式
date: 2020-12-21
categories:
  - frond-end
tags :
  - design patterns
---

构造函数(构造器)：初始化
构造器模式：使用构造函数去初始化对象
### 举个栗子
```js
function User(name,age,career){
  this.name = name
  this.age = age
}

const XiaoMing = new User('xiaoming',28)
```
在创建一个user过程中，变的是每个user的姓名、年龄、工种这些值，这是用户的个性，不变的是每个员工都具备姓名、年龄、工种这些属性，这是用户的共性。

构造器将 name、age、career 赋值给对象的过程封装，确保了每个对象都具备这些属性，确保了共性的不变，同时将 name、age、career 各自的取值操作开放，确保了个性的灵活。

使用工厂模式时，我们要做的就是去抽象不同构造函数（类）之间的变与不变。

### 简单工厂
```js
// function User(name,career){
//   this.name = name
//   this.career = career
//   // this.work = work
//   switch(career){
//     case 'manager':
//       this.work = 'XXX'
//       break
//     case 'XX':
//       this.work = 'XXX'
//       break
//     // ...
//   }
// }
// 构造函数Factory职责是判断角色并添加职责分配，然后调用User。User只是创建对象（无脑传参），保持了“封闭”原则。Factory可以随时应对修改，保持“开放”原则。如果只是使用User的话，需求一旦改变，User就会改变。只满足“开放”，不满足“封闭”原则。
function User(name, career, work){
  this.name = name
  this.career = career
  this.work = work
}
function Factroy(name, career, work){
  let work
  switch(career){
    case 'manager':
      work = 'XXX'
      break
    case 'XX':
      twork = 'XXX'
      break
    // ...
  }
  return new User(name,career,work)
}
//switch可以考虑换成map
```

工厂模式：工厂模式其实就是将创建对象的过程单独封装。
应用场景：有构造函数的地方，我们就应该想到简单工厂；在写了大量构造函数、调用了大量的 new、自觉非常不爽的情况下，我们就应该思考是不是可以掏出工厂模式重构我们的代码了。
构造器解决的是多个对象实例的问题，简单工厂解决的是多个类的问题。

评论
Java工厂模式有接口，然后有一些class去实现这个接口，再用一个工厂类对外服务，根据“变化部分”生成这些class的实例。因为这些实例都实现了同一个接口，所以我们可以无所顾忌对这些实例使用接口方法。
切换到JavaScript。JS没有接口，严格上说也没有class。所有对象，不论是字面量对象，还是通过构造函数生成的对象，它们都是一个红黑树维护数据结构（不是所有的JS引擎都是使用红黑树）。同样是根据“变化部分”——构造函数的参数去生成不同的对象，只要我们在编码时候保证这些对象拥有同样的“接口方法”，那我们就可以放心使用这些对象。（这里的“接口方法”应该就是User构造函数，类似java里不同的类实现的同一个接口，是共性不变的部分）

### AbstractFactory抽象工厂
```js
var dog = new Dog()
 console.log(dog.sound('汪汪汪'))  // 汪汪汪
 var cat = new Cat()
 console.log(cat.sound('喵喵喵'))  // 喵喵喵
 ```


面向对象和函数式编程都是一种编程思维
面向对象：当我们在代码中用对象表示实体时，就是所谓的 面向对象编程，简称为 “OOP”。

开放封闭原则的内容：对拓展开放，对修改封闭。

### 举个栗子
如下类，仅约定手机流水线的通用能力。如果尝试让它，比如 new 一个 MobilePhoneFactory 实例，并尝试调用它的实例方法。就会报错，提醒“我不是让你拿去new一个实例的，我就是个定规矩的”。
在抽象工厂模式里，该类就是我们食物链顶端最大的 Boss——AbstractFactory（抽象工厂）。
抽象工厂不干活，具体工厂（ConcreteFactory）来干活！
```js
class MobilePhoneFactory {
    // 提供操作系统的接口
    createOS(){
        throw new Error("抽象工厂方法不允许直接调用，你需要将我重写！");
    }
    // 提供硬件的接口
    createHardWare(){
        throw new Error("抽象工厂方法不允许直接调用，你需要将我重写！");
    }
}
```
比如我现在想要一个专门生产 Android 系统 + 高通硬件的手机的生产线，我给这类手机型号起名叫 FakeStar，那我就可以为 FakeStar 定制一个具体工厂：
```js
class FakeStar extends MobilePhoneFactory{
  createOS(){
     // 提供安卓系统实例
    return new AndroidOS()
  }
  createHardWare(){
    // 提供高通硬件实例
    return new QualcommHardWare()
  }
}
```
在提供安卓系统的时候，调用了两个构造函数：AndroidOS 和 QualcommHardWare，它们分别用于生成具体的操作系统和硬件实例。像这种被我们拿来用于 new 出具体对象的类，叫做具体产品类（ConcreteProduct）。具体产品类往往不会孤立存在，不同的具体产品类往往有着共同的功能，比如安卓系统类和苹果系统类，它们都是操作系统，都有着可以操控手机硬件系统这样一个最基本的功能。因此我们可以用一个抽象产品（AbstractProduct）类来声明这一类产品应该具有的基本功能
```js
// 定义操作系统这类产品的抽象产品类
class OS {
    controlHardWare() {
        throw new Error('抽象产品方法不允许直接调用，你需要将我重写！');
    }
}

// 定义具体操作系统的具体产品类
class AndroidOS extends OS {
    controlHardWare() {
        console.log('我会用安卓的方式去操作硬件')
    }
}

class AppleOS extends OS {
    controlHardWare() {
        console.log('我会用🍎的方式去操作硬件')
    }
}
//...

//硬件产品类同理
// 定义手机硬件这类产品的抽象产品类
class HardWare {
    // 手机硬件的共性方法，这里提取了“根据命令运转”这个共性
    operateByOrder() {
        throw new Error('抽象产品方法不允许直接调用，你需要将我重写！');
    }
}

// 定义具体硬件的具体产品类
class QualcommHardWare extends HardWare {
    operateByOrder() {
        console.log('我会用高通的方式去运转')
    }
}

class MiWare extends HardWare {
    operateByOrder() {
        console.log('我会用小米的方式去运转')
    }
}
//...
```
生成一部FakeStar
```js
const newPhone = new FakeStarFactroy()
const os = newPhone.createOS()
const hardWare = newPhone.createHardWare()
os.controlHardWare()
hardWare = operateByOrder()
```
关键的时刻来了——假如有一天，FakeStar过气了，我们需要产出一款新机投入市场，这时候怎么办？我们是不是不需要对抽象工厂MobilePhoneFactory做任何修改，只需要拓展它的种类：
```js
class newStarFactory extends MobilePhoneFactory {
    createOS() {
        // 操作系统实现代码
    }
    createHardWare() {
        // 硬件实现代码
    }
}
```
### 总结
- 抽象工厂（抽象类，它不能被用于生成具体实例）： 用于声明最终目标产品的共性。在一个系统里，抽象工厂可以有多个（大家可以想象我们的手机厂后来被一个更大的厂收购了，这个厂里除了手机抽象类，还有平板、游戏机抽象类等等），每一个抽象工厂对应的这一类的产品，被称为“产品族”。
- 具体工厂（用于生成产品族里的一个具体的产品）： 继承自抽象工厂、实现了抽象工厂里声明的那些方法，用于创建具体的产品的类。
- 抽象产品（抽象类，它不能被用于生成具体实例）： 上面我们看到，具体工厂里实现的接口，会依赖一些类，这些类对应到各种各样的具体的细粒度产品（比如操作系统、硬件等），这些具体产品类的共性各自抽离，便对应到了各自的抽象产品类。
- 具体产品（用于生成产品族里的一个具体的产品所依赖的更细粒度的产品）： 比如我们上文中具体的一种操作系统、或具体的一种硬件等。

抽象工厂是不是高内聚，低耦合的具体实现，抽象工厂负责所有核心功能的定义且不允许修改，把具体业务的定义交给具体工厂去执行。同时各个业务之间的几无依赖，业务分离，可维护性和扩展性大大增强。最有利的就是不会修改引入，方便阅读。

## 实例：项目中的轮播组件封装
场景：每个轮播页内会有不同组件，会进行统一管理，翻页时也会有重置动作
```js
//抽象类
(function (global) {
	var _FXInterface = function () {

	};
	_FXInterface.prototype = {
		init: function () {
			throw new Error("init 方法需要在子类中重写！");
		},
		reset: function () {
			throw new Error("reset 方法需要在子类中重写！");
		},
		destroy: function () {
			throw new Error("destroy 方法需要在子类中重写！");
		}
	};
	global.FXInterface = _FXInterface;
})(this, undefined);

//继承方法
function inherit(sup, sub) {
  var _create = Object.create || function (_prototype) {
    var f = function () {
    };
    f.prototype = _prototype;
    return new f();
  };
  sub.prototype = _create(sup.prototype);
  sub.prototype.constructor = sub;
}

//声明页面内组件并继承抽象类
var demoComponent = function(id,option){
  this.id = id
  this.options = option
  //...
}
inherit(FXInterface,demoComponent)
```
### 使用
```js
//统一管理页面内所有组件,观察者模式
(function (global) {
	var _FXH5 = function (options) {
    //...
  };

	_FXH5.prototype = {
		init: function (options, loadSinglePage) {
			//...会调用收集到的所有组件的init方法
    },
    reset: function(){
      //...会调用收集到的所有组件的reset方法
    },
    destroy: function(){
      //...会调用收集到的所有组件的destroy方法
    }
	};
	global.FXH5 = _FXH5;

})(this, undefined);

//使用
window.fx = new FXH5(fx_options);
fx.init()
function swiper(){
  if(xxx){
    //播放当前页面
    fx.reset()
  }else{
    //结束播放（退出页面）
    fx.destroy()
  }
}
```

