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
 * @param start 可选。从该位置开始读取数据，默认为 0。如果为负值，表示倒数。
 * @param end 可选。到该位置前停止读取数据，默认等于数组长度。使用负数可从数组结尾处规定位置。（不包含本身）
 * @return {*}
 */
array.copyWithin(target, start = 0, end = this.length)

let arr = [1, 2, 3, 4, 5]
arr.copyWithin(1, 2, 4) // [1, 3, 4, 4, 5]  注意：数组长度不会改变
arr.copyWithin(0, -2, -3) // [1, 2, 3, 4, 5] end索引小于等于start 相当于什么都没复制
arr.copyWithin(0, -4, -2) // [2, 3, 3, 4, 5] 
```
`fill`


### 遍历
推荐for 性能最好
```js
//for
for(let i=0;i<arr.length;i++){}
//forEach
arr.forEach((item,index)=>{})
//map：对每个元素进行处理，返回一个全新的数组
const newArr = arr.map((item,index)=>{
  return item+1
})
```
## 二维数组/矩阵
### 创建
不能用fill创建
fill:如果入参类型是引用类型，那么填充的内容就是引用类型的
```js
const arr = new Array(2).fill([])
arr[0][0] = 1
console.log(arr)
//[[1],[1]]
```
for循环
```js
for(let i=0;i<len;i++){
  arr[i] = []
}
```
### 访问
```js
const outerLen = arr.length
for(let i=0;i<outerLen;i++>){
  const innerLen = arr[i].length
  for(let j=0;j<innerLen;j++){

  }
}
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
