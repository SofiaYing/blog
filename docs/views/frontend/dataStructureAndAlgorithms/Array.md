---
title: Array
date: 2020-12-24
categories:
  - frond-end
tags :
  - dataStructure
---

## 创建
**字面量**
```js
const arr = [] 
const arr = [3, 8, 11]
```
**构造器**
```js
// 一维数组
const arr = new Array() // []
const arr = new Array(3) // 长度为3的数组 [ , , ]
const arr = new Array(3).fill(0) // [0, 0, 0]
const arr = new Array(3, 8, 11) // [3, 8, 11]
// 二维数组
const arr = new Array(2).fill([])
arr[0][0] = 1
```
**ES6 Array.of()** 返回由所有参数值组成的数组
```js
const arr = Array.of(3) // [3]
const arr = Array.of(3, 11, 8) // [3, 11, 8]
```
**ES6 Array.from()** 
- 对一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例。
- 类数组：对象拥有length属性
- 可迭代对象：部署了Iterator接口的数据结构，比如:字符串、Set、NodeList对象
```js
// 1. 对象拥有length属性
let obj = {0: 'a', 1: 'b', 2:'c', length: 3};
let arr = Array.from(obj); // ['a','b','c'];
// 2. 部署了 Iterator接口的数据结构 比如:字符串、Set、NodeList对象
let arr = Array.from('hello'); // ['h','e','l','l','o']
let arr = Array.from(new Set(['a','b'])); // ['a','b']
```
`补充`：类数组，不能直接使用数组方法，但你可以像使用数组那样，使用类数组。
《javascript权威指南》上给出了代码用来判断一个对象是否属于“类数组”。如下：
```js
// Determine if o is an array-like object.
// Strings and functions have numeric length properties, but are 
// excluded by the typeof test. In client-side JavaScript, DOM text
// nodes have a numeric length property, and may need to be excluded 
// with an additional o.nodeType != 3 test.
function isArrayLike(o) {
    if (o &&                                // o is not null, undefined, etc.
        typeof o === 'object' &&            // o is an object
        isFinite(o.length) &&               // o.length is a finite number
        o.length >= 0 &&                    // o.length is non-negative
        o.length===Math.floor(o.length) &&  // o.length is an integer
        o.length < 4294967296)              // o.length < 2^32
        return true;                        // Then o is array-like
    else
        return false;                       // Otherwise it is not
}
```
类数组转为数组 
- Array.prototype.slice.call(arrLike)
- Array.from(arrLike)

## 方法
改变原数组的值，不会改变原数组，以及数组的遍历方法
### 改变原数组的方法（9个）
- ES5: splice / sort / pop / push / shift / unshift / reverse
- ES6: copyWithin / fill
**注意**：对于这些能够改变原数组的方法，要**注意避免在循环遍历中改变原数组的选项**，比如: 改变数组的长度，导致遍历的长度出现问题。

- `splice` 添加/删除数组元素
英文释义：胶接，粘接
```js
/**
 * @param index：必需。整数，规定添加/删除项目的位置，使用负数可从数组结尾处规定位置。
 * @param howmany：可选。要删除的项目数量。如果设置为 0，则不会删除项目。如果未规定此参数，则删除从 index 开始到原数组结尾的所有元素。数组如果元素不够，会删除到最后一个元素为止。
 * @param item1, ..., itemX： 可选。向数组添加的新项目。
 * @return 含有被删除的元素的数组
 */
let arr = [1, 2, 3, 4, 5, 6]
arr.splice(4)  // [5, 6]  arr [1, 2, 3, 4]
arr.splice(1, 1) // [3]  arr [1, 3, 4]
arr.splice(0, 0, 1) // []  arr [1, 1 ,3, 4]
arr.splice(-1, 2, 'add', 2) // [4]  arr [1, 1, 3, 'add', 2] 
```
- `sort` 对数组元素进行排序，并返回这个数组
```js
/**
 * @param compareFunction 可选。用来指定按某种顺序进行排列的函数。如果省略，元素会按照调用toString()方法转换为的字符串的各个字符的Unicode位点进行排序。
  - firstEl: 第一个用于比较的元素。
  - secondEl: 第二个用于比较的元素。
  这两个参数是数组中两个要比较的元素，通常我们用 a 和 b 接收两个将要比较的元素：
  - 若比较函数返回值<0，那么a将排到b的前面;
  - 若比较函数返回值=0，那么a 和 b 相对位置不变；
  - 若比较函数返回值>0，那么b 排在a 将的前面；
 * @return 排序后的数组
 */
let arr = [10, 1, 3, 20,25,8];
arr.sort()  // [1,10,20,25,3,8];
```
**数组元素为数字的升序/降序**
```js
// 数组元素为数字的升序
arr.sort(function(a,b){  // 第一次输出，a相当于1，b相当于10
   return a-b; // b是小数，想前排，就要返回>0的数，所以a-b
});  // [1, 3, 8, 10, 20, 25]
// 数组元素为数字的降序
arr.sort(function(a,b){
   return b-a; // b是大数，想前排，要返回>0的数，所以b-a
});  // [1, 3, 8, 10, 20, 25]
```
**复杂数组**
```js
let array = [{id:10,age:2},{id:5,age:4},{id:6,age:10},{id:9,age:6},{id:2,age:8}{id:10,age:9}];
array.sort(function(a,b){
    if(a.id === b.id){// 如果id的值相等，按照age的值降序
        return b.age - a.age
    }else{ // 如果id的值不相等，按照id的值升序
        return a.id - b.id
    }
})
// [{"id":2,"age":8},{"id":5,"age":4},{"id":6,"age":10},{"id":9,"age":6},{"id":10,"age":9},{"id":10,"age":2}] 
```
**自定义比较函数**
```js
let array = [{name:'Koro1'},{name:'Koro1'},{name:'OB'},{name:'Koro1'},{name:'OB'},{name:'OB'}];
// name为'Koro1'往前排
array.sort(function(a,b){
  if(b.name === 'Koro1') { 
    return 1
  } else {
    return -1
  }
})
// [{"name":"Koro1"},{"name":"Koro1"},{"name":"Koro1"},{"name":"OB"},{"name":"OB"},{"name":"OB"}] 
```
- `pop` / `shift` 无参数
- `push` / `unshift`  参数: item1, item2, ..., itemX 
```js
let arr = [1, 2]
arr.unshift(2, 3) // [2, 3, 1, 2]
```
- `reverse` 颠倒数组中元素的顺序 无参数
- `copyWithin` 方法浅复制数组的一部分到同一数组中的另一个位置，并返回它，不会改变原数组的长度。
```js
/**
 * @param target 必需。从该位置开始替换数据。如果为负值，表示倒数。
 * @param start 可选。从该位置开始读取数据，默认为 0。如果为负值，表示倒数(开始索引会被自动计算成为 length+start)。
 * @param end 可选。到该位置前停止读取数据，默认等于数组长度。使用负数可从数组结尾处规定位置。（不包含本身）
 * @return {*}
 */
array.copyWithin(target, start = 0, end = this.length)

let arr = [1, 2, 3, 4, 5]
arr.copyWithin(1, 2, 4) // [1, 3, 4, 4, 5]  注意：数组长度不会改变
arr.copyWithin(0, -2, -3) // [1, 2, 3, 4, 5] end索引小于等于start 相当于什么都没复制
arr.copyWithin(0, -4, -2) // [2, 3, 3, 4, 5] 
```
`fill` 用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引。
```js
array.fill(value, start = 0, end = this.length)
['a', 'b', 'c'].fill(7) // [7, 7, 7]
['a', 'b', 'c'].fill({age: 12}, 1, 2) // ['a', {age: 12}, 'c']  注：当一个对象被传递给 fill方法的时候, 填充数组的是这个对象的引用。
```
### 不改变原数组的方法（8个）
- ES5: slice / join / toString / toLocaleString / concat / indexOf / lastIndexOf
- ES7: includes

`slice` 返回一个新的数组对象，这一对象是一个由 begin 和 end 决定的原数组的**浅拷贝**（包括 begin，不包括end）。原始数组不会被改变。
英文释义：切；切片；割
**注**：字符串 slice(beginIndex[, endIndex])方法，提取某个字符串的一部分，并返回一个新的字符串，且不会改动原字符串。
```js
/**
 * @description: 
 * @param begin
 * @param end
 * @return 一个新的数组对象
 */
array.slice(begin, end);

let a= ['hello','world'];
let b=a.slice(0,1); // ['hello']
a[0]='改变原数组';
console.log(a,b); // ['改变原数组','world'] ['hello']
b[0]='改变拷贝的数组';
console.log(a,b); // ['改变原数组','world'] ['改变拷贝的数组']

let a = [{name:'OBKoro1'}];
let b = a.slice();
console.log(b,a); // [{"name":"OBKoro1"}]  [{"name":"OBKoro1"}]
a[0].name = '改变原数组';
console.log(b,a); // [{"name":"改变原数组"}] [{"name":"改变原数组"}]
b[0].name = '改变拷贝数组', b[0].koro='改变拷贝数组'; //[{"name":"改变拷贝数组","koro":"改变拷贝数组"}] [{"name":"改变拷贝数组","koro":"改变拷贝数组"}]
```
`join` 把数组中的所有元素通过指定的分隔符进行分隔放入一个字符串，返回生成的字符串。
**注**：split(英文释义：分裂，分离) String方法，使用指定的分隔符字符串将一个String对象分割成子字符串数组。
```js
let a = ['hello','world'];
let str = a.join(); // 'hello,world'
let str = a.join('+'); // 'hello+world'

// join()/toString()方法在数组元素是数组的时候，会将里面的数组也调用join()/toString(),如果是对象的话，对象会被转为[object Object]字符串。
let a = [['OBKoro1','23'],'test'];
let str1 = a.join(); // "OBKoro1,23,test"
let b = [{name:'OBKoro1',age:'23'},'test'];
let str2 = b.join(); // "[object Object],test"
// 对象转字符串推荐JSON.stringify(obj);
```
`toString` 可把数组转换为由逗号链接起来的字符串, 更推荐join
`toLocaleString` 返回格式化对象后的字符串，该字符串格式因不同语言而不同
```js
const array1 = [1, 'a', new Date('21 Dec 1997 14:12:00 UTC')];
const localeString = array1.toLocaleString('en', { timeZone: 'UTC' });

console.log(localeString);
// expected output: "1,a,12/21/1997, 2:12:00 PM",
// This assumes "en" locale and UTC timezone - your results may vary
```
`concat` 合并两个或多个数组，返回一个新数组
```js
/**
 * @param arrayX（必须）：该参数可以是具体的值，也可以是数组对象。可以是任意多个。
 */
var newArr = oldArray.concat(arrayX, arrayX, ......, arrayX)

let a = [1, 2, 3];
let b = [4, 5, 6];
//连接两个数组
let newVal=a.concat(b); // [1,2,3,4,5,6]
// 连接三个数组
let c = [7, 8, 9]
let newVal2 = a.concat(b, c); // [1,2,3,4,5,6,7,8,9]
// 添加元素
let newVal3 = a.concat('添加元素',b, c,'再加一个'); 
// [1,2,3,"添加元素",4,5,6,7,8,9,"再加一个"]
// 合并嵌套数组  会浅拷贝嵌套数组
let d = [1, 2];
let f = [3, [4]];
let newVal4 = d.concat(f); // [1,2,3,[4]]
```
**ES6 扩展运算符**， 更方便合并数组的方法
```js
let a = [1, 2, 3]
let b = [1, ...a, 4, 4]  // [1, 1, 2, 3, 4, 4]
```
`indexOf` 返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。
**注** 
- 严格相等的搜索:数组的indexOf搜索跟字符串的indexOf不一样,数组的indexOf使用严格相等===搜索元素，即数组元素要完全匹配才能搜索成功。
- indexOf()不能识别NaN
**使用场景**
- 数组去重：遍历，将值添加到新数组，用indexOf()判断值是否存在，已存在就不添加，达到去重效果。
- 根据获取的数组下标执行操作，改变数组中的值等。
- 判断是否存在，执行操作。
```js
/**
 * @param searchElement (必须):被查找的元素
 * @param fromIndex (可选):开始查找的位置(不能大于等于数组的长度，返回-1)，接受负值，默认值为0。
 */
array.indexOf(searchElement,fromIndex)

let a = [NaN, '数据', 1]
a.indexOf(NaN) // -1
a.indexOf(1) // 2
```
`lastIndexOf` 返回指定元素,在数组中的最后一个的索引，如果不存在则返回 -1。（从数组后面往前查找）
```js
/**
 * @param searchElement(必须): 被查找的元素
 * @param fromIndex(可选): 从此位置开始逆向查找，默认值数组的长度-1，即查找整个数组。
 * 关于fromIndex有三个规则:
 * - 正值。如果该值大于或等于数组的长度，则整个数组会被查找。
 * - 负值。将其视为从数组末尾向前的偏移。(比如-2，从数组最后第二个元素开始往前查找)
 * - 负值。其绝对值大于数组长度，则方法返回 -1，即数组不会被查找。
 */
arr.lastIndexOf(searchElement,fromIndex)
let a = [1,1,1,1,1,1]
a.lastIndexOf(1,10) // 5
a.lastIndexOf(1,-2) // 4
```
`includes` 查找数组是否包含某个元素 返回布尔
是为了弥补indexOf方法的缺陷而出现的
- indexOf方法不能识别NaN
- indexOf方法检查是否包含某个值不够语义化，需要判断是否不等于-1，表达不够直观
```js
/**
 * @param searchElement(必须):被查找的元素
 * @param fromIndex(可选):默认值为0，参数表示搜索的起始位置，接受负值。正值超过数组长度，数组不会被搜索，返回false。负值绝对值超过长数组度，重置从0开始搜索。
 */
array.includes(searchElement,fromIndex=0)

let a=['OB','Koro1',1,NaN];
let b=a.includes(NaN); // true 识别NaN
let b=a.includes('Koro1',100); // false 超过数组长度 不搜索
let b=a.includes('Koro1',-3);  // true 从倒数第三个元素开始搜索 
let b=a.includes('Koro1',-100);  // true 负值绝对值超过数组长度，搜索整个数组
```


## 遍历
- ES5: forEach / every / some / filter / map / reduce / reduceRight 
- ES6: find / findIndex / keys / values / entries
**注**
- 尽量不要在遍历的时候，修改后面要遍历的值
- 尽量不要在遍历的时候修改数组的长度（删除/添加）
**注**遍历推荐for 性能最好（while也一样）

`forEach` 按升序为数组中含有效值的每一项执行一次回调函数。
```js
/**
 * @param function(必须): 数组中每个元素需要调用的函数。
    // 回调函数的参数
    1. currentValue(必须),数组当前元素的值
    2. index(可选), 当前元素的索引值
    3. arr(可选),数组对象本身
  * @param thisValue(可选): 当执行回调函数时this绑定对象的值，默认值为undefined
 */
array.forEach(function(item, index, arr), thisValue)
```
**注**
- 无法中途退出循环，只能用return退出本次回调，进行下一次回调。（因为return只会退出当前函数，即当前运行到的传入的回调函数，而不是指跳出forEach）
- 它总是返回 undefined值,即使你return了一个值。
**补充1**
1. 对于空数组是不会执行回调函数的
2. 对于已在迭代过程中删除的元素，或者空元素会跳过回调函数
3. 遍历次数再第一次循环前就会确定，再添加到数组中的元素不会被遍历。
4. 如果已经存在的值被改变，则传递给 callback 的值是遍历到他们那一刻的值。
**补充2**
- for循环是可以break,continue,return打断的
- 注意return要写在函数体内，如果只有for循环，未被包裹到函数内，return会报错
```js
let a = [1, 2, ,3]; // 最后第二个元素是空的，不会遍历(undefined、null会遍历)
let obj = { name: 'OBKoro1' };
let result = a.forEach(function (value, index, array) { 
  a[3] = '改变元素';
  a.push('添加到尾端，不会被遍历')
  console.log(value, 'forEach传递的第一个参数'); // 分别打印 1 ,2 ,改变元素
  console.log(this.name); // OBKoro1 打印三次 this绑定在obj对象上
  // break; // break会报错
  return value; // return只能结束本次回调 会执行下次回调
  console.log('不会执行，因为return 会执行下一次循环回调')
}, obj);
console.log(result); // 即使return了一个值,也还是返回undefined
// 回调函数也接受接头函数写法
```

`every` 检测数组所有元素是否都符合判断条件
```js
/**
 * @param 同forEach
 * @return 
 * 1. 如果数组中检测到有一个元素不满足，则整个表达式返回 false，且剩余的元素不会再进行检测。
 * 2. 如果所有元素都满足条件，则返回 true。
 */
array.every(function(currentValue, index, arr), thisValue);

let arr = [1, 2, 3] 
function cb(value) {
  return value > 2
}
arr.every(cb)  //false
```

`some` 数组中的是否有满足判断条件的元素
```js
/**
 * @param 同forEach
 * @return 
 * 1. 如果有一个元素满足条件，则表达式返回true, 剩余的元素不会再执行检测。
 * 2. 如果没有满足条件的元素，则返回false。
 */
array.some(function(currentValue, index, arr), thisValue)

let arr = [1, 2, 3] 
function cb(value) {
  return value > 2
}
arr.every(cb)  //true
```

`filter` 过滤原始数组，返回新数组。返回一个新数组, 其包含通过所提供函数实现的测试的所有元素。
```js
let new_array = arr.filter(function(currentValue, index, arr), thisArg);

let arr = [1, 2, 3] 
function cb(value) {
  return value > 2
}
let result = arr.filter(cb) 
console.log(result, arr)// [3]  [1, 2, 3]
```

`map` 对数组中的每个元素进行处理，返回新的数组
```js
let new_array = arr.map(function(currentValue, index, arr), thisArg);

let arr = [1, 2, 3] 
function cb(value) {
  return value + 2
}
let result = arr.map(cb) 
console.log(result, arr)// [3, 4, 5]  [1, 2, 3]
```

`reduce` reduce 为数组提供累加器，合并为一个值。
**回调第一次执行时:**
- 如果 initialValue 在调用 reduce 时被提供，那么第一个 total 将等于 initialValue，此时 currentValue 等于数组中的第一个值；
- 如果 initialValue 未被提供，那么 total 等于数组中的第一个值，currentValue 等于数组中的第二个值。此时如果数组为空，那么将抛出 TypeError。
- 如果数组仅有一个元素，并且没有提供 initialValue，或提供了 initialValue 但数组为空，那么回调不会被执行，数组的唯一值将被返回。
```js
/**
 * @description: 对累加器和数组中的每个元素（从左到右）应用一个函数，最终合并为一个值。
 * @param function(必须): 数组中每个元素需要调用的函数。
 * // 回调函数的参数
 * 1. accumulator (必须) 累计器，初始值, 或者上一次调用回调返回的值
 * 2. currentValue(必须),数组当前元素的值
 * 3. currentIndex (可选), 当前元素的索引值
 * 4. array (可选),数组对象本身
 * @param initialValue(可选): 指定第一次回调 的第一个参数。
 */
array.reduce(function(accumulator, currentValue, currentIndex, arr), initialValue;

let maxCallback = ( acc, cur ) => Math.max( acc.x, cur.x );
let maxCallback2 = ( max, cur ) => Math.max( max, cur );

// reduce() 没有初始值
[ { x: 2 }, { x: 22 }, { x: 42 } ].reduce( maxCallback ); // NaN
[ { x: 2 }, { x: 22 }            ].reduce( maxCallback ); // 22
[ { x: 2 }                       ].reduce( maxCallback ); // { x: 2 }
[                                ].reduce( maxCallback ); // TypeError

// 将二维数组转化为一维 将数组元素展开
let flattened = [[0, 1], [2, 3], [4, 5]].reduce(
  (a, b) => a.concat(b),
  [1, 2]
);  // [1, 2, 0, 1, 2, 3, 4, 5]
```

`reduceRight` 从右至左累加, 其他参考reduce方法。

`find` 用于找出第一个符合条件的数组成员，并返回该成员，如果没有符合条件的成员，则返回undefined。
`findIndex`返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。
**注：**这两个方法都可以识别NaN,弥补了indexOf的不足。
```js
let new_array = arr.find(function(currentValue, index, arr), thisArg);
let new_array = arr.findIndex(function(currentValue, index, arr), thisArg);

// find
let a = [1, 4, -5, 10].find((n) => n < 0); // 返回元素-5
let b = [1, 4, -5, 10, NaN].find((n) => Object.is(NaN, n));  // 返回元素NaN
// findIndex
let a = [1, 4, -5, 10].findIndex((n) => n < 0); // 返回索引2
let b = [1, 4, -5, 10, NaN].findIndex((n) => Object.is(NaN, n));  // 返回索引4
```

`keys()&values()&entries()` 遍历键名、遍历键值、遍历键名+键。三个方法都返回一个新的 **Array Iterator 对象**，对象根据方法不同包含不同的值。
```js
array.keys()
array.values()
array.entries()

for (let index of ['a', 'b'].keys()) {
  console.log(index);
} //'0' '1'

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
} // 'a' 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
} // '0' 'a'  '1' 'b'
```
**补充1** 在for..of中如果遍历中途要退出，可以使用break退出循环。
**补充2** 如果不使用for...of循环，可以手动调用遍历器对象的next方法，进行遍历。
```js
var arr = ['a', 'b', 'c', 'd', 'e'];
var iterator = arr.values();
iterator.next();               // Object { value: "a", done: false }
iterator.next().value;         // "b"
iterator.next()["value"];      // "c"
iterator.next();               // Object { value: "d", done: false }
iterator.next();               // Object { value: "e", done: false }
iterator.next();               // Object { value: undefined, done: true }
iterator.next().value;         // undefined
```
**补充3** 索引迭代器会包含那些没有对应元素的索引
```js
var arr = ["a", , "c"];
var sparseKeys = Object.keys(arr);
var denseKeys = [...arr.keys()];
console.log(sparseKeys); // ['0', '2']
console.log(denseKeys);  // [0, 1, 2]
```

### 增加元素
- unshift: 头插入
- push: 尾插入
- splice: 任意位置
array.splice(起始index,删除长度,要添加到数组的新元素)
```js
const arr = [1,2,3]
const delArr = arr.slice(1,0,5)  
//arr [1,5,2,3]
//delArr []
```
### 删除元素
- shift: 头删除
- pop: 尾删除
- slice: 删除任意位置元素
```js
const arr = [1,2,3]
const delArr = arr.slice(1,2)
//arr [1]
//delArr [2,3]
```
两数求和问题：
真题描述： 给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

```js
var arr = [1,2,3,4]
var target = 4
var sum = function(arr,target){
  var len = arr.length
  for(var i=0;i<len;i++){
    for(var j=0;j<len;j++){
      if(arr[i]+arr[j] === target){
        return [i,j]
      }
    }
  }
}
```
** 记忆点：几乎所有的求和问题，都可以转化为求差问题 **

可以在遍历数组的过程中，增加一个 Map 来记录已经遍历过的数字及其对应的索引值。然后每遍历到一个新数字的时候，都回到 Map 里去查询 targetNum 与该数的差值是否已经在前面的数字中出现过了。若出现过，那么答案已然显现

两层循环转换为一层循环

利用Map
```js
const twoSum = function(arr,target){
  let len = arr.length
  let map = {}
  for(let i=0;i<len;i++){
    if(map[target-arr[i]] !== 'undefined'){
      return [map[target-arr[i]],i]
    }
    map[arr[i]] = i
  }
}
//ES6
let map = new Map()
//...
map.set(arr[i],i)
map.get(target-arr[i])
```

合并两个有序数组：
真题描述：给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6], n = 3
```js
const merge = function(arr1,arr2,m,n){
  let i = m - 1
  let j = n - 1
  let k = m + n - 1
  while(j>=0 && i>=0){
    if(arr2[j]>=arr1[i]){
      arr1[k] = arr2[j]
      k--
      j--
    }else{
      arr1[k] = arr1[i]
      i--
      k--
    }
  }

  while(j>=0){
    arr1[k] = arr2[j]  
    k-- 
    j--
  }
}
```

真题描述：给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

三数之和延续两数之和的思路，我们可以把求和问题变成求差问题——固定其中一个数，在剩下的数中寻找是否有两个数和这个固定数相加是等于0的。

虽然乍一看似乎还是需要三层循环才能解决的样子，不过现在我们有了双指针法，定位效率将会被大大提升，从此告别过度循环

双指针法的使用场景了，一方面，它可以做到空间换时间；另一方面，它也可以帮我们降低问题的复杂度。）

双指针法用在涉及求和、比大小类的数组题目里时，大前提往往是：该数组必须有序。否则双指针根本无法帮助我们缩小定位的范围，压根没有意义。
```js
function sum(arr,target){
  let res = []
  let len = arr.length

  arr.sort(function(a,b){
    return a-b
  })

  for(let i=0;i<len-2;i++){
    let curValue = arr[i]
    let m=i+1
    let n=len-1
    if(i>0&&arr[i]===arr[i-1]){
      continue
    }
    while(n>m){
      if(curValue+arr[m]+arr[n] < target){
        m++
        // while(m<n && arr[m]===arr[m-1]){
        //   m++
        // }
      }else if(curValue+arr[m]+arr[n] > target){
        n--
        // while(m<n && arr[n]===arr[n+1]){
        //   n--
        // }
      }else{
        res.push([arr[i],arr[m],arr[n]])
        m++
        n--
        while(m<n&&nums[m]===nums[m-1]) {
          m++
        }  
        while(m<n&&nums[n]===nums[n+1]) {
          n--
        }
      }
    }
  }
}
```

什么时候你需要联想到对撞指针？
这里我给大家两个关键字——“有序”和“数组”。
没错，见到这两个关键字，立刻把双指针法调度进你的大脑内存。普通双指针走不通，立刻想对撞指针！

即便数组题目中并没有直接给出“有序”这个关键条件，我们在发觉普通思路走不下去的时候，也应该及时地尝试手动对其进行排序试试看有没有新的切入点——没有条件，创造条件也要上。

![【干货】js 数组详细操作方法及解析合集](https://juejin.cn/post/6844903614918459406)
