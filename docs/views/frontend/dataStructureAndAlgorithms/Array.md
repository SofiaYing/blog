---
title: Array
date: 2020-12-24
categories:
  - frond-end
tags :
  - dataStructure
---
## 数组的创建 （推荐的方法）
事实上，在 JavaScript 数据结构中，数组几乎是“基石”一般的存在。
**一维数组**
```js
const arr = [1, 2, 3, 4]
const arr = new Array() //const arr = []
const arr = new Array(7)
const arr = (new Array(7)).fill(1)
```
**二维数组**
```js
// 不存在引用问题
const len = arr.length
for(let i=0;i<len;i++) {
    // 将数组的每一个坑位初始化为数组
    arr[i] = []
}
```
```js
// 存在引用问题的方法
const arr = new Array(3).fill([])
arr[0][0] = 1 // [[1],[1],[1]] 会发现每一项都被填充了值
// 当给 fill 传递一个入参时，如果这个入参的类型是引用类型，那么 fill 在填充坑位时填充的其实就是入参的引用。
// 其实这3个数组对应了同一个引用、指向的是同一块内存空间，它们本质上是同一个数组。因此当你修改第0行第0个元素的值时，第1-2行的第0个元素的值也都会跟着发生改变。
```
## 数组的遍历 （推荐的方法）
**一维数组**
1. for
- for循环可以用break,continue,return打断
- 注意return要写在函数体内，如果只有for循环，未被包裹到函数内，return会报错
2. forEach
- 无法中途退出循环，只能用return退出本次回调，进行下一次回调。（因为return只会退出当前函数，即当前运行到的传入的回调函数，而不是指跳出forEach）
- 它总是返回 undefined值,即使你return了一个值。
3. map
map 方法在调用形式上与 forEach 无异，区别在于 map 方法会根据你传入的函数逻辑对数组中每个元素进行处理、进而返回一个全新的数组。
所以其实 map 做的事情不仅仅是遍历，而是在遍历的基础上“再加工”。当我们需要对数组内容做批量修改、同时修改的逻辑又高度一致时，就可以调用 map 来达到我们的目的
```js
var arr = [1, 2, 3]
var res = arr.map((item, index)=>{
  return item++
})
```
**二维数组**
1. for
```js
for (let i = 0; i < arr.length; i++) {
  for (let j = 0; j < arr[i].length; j++) {
    console.log(arr[i][j])
  }
}
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
