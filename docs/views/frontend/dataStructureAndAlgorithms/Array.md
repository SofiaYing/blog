---
title: Array
date: 2020-12-24
categories:
  - frond-end
tags :
  - dataStructure
---

## 一维数组
### 创建
```js
const arr = [1,2]
```
算法题中很多不知道数据的内部元素推荐：
```js
//创建长度为8的数组
const arr = new Array(8)
//创建长度为8，且数值均为0的数组
const arr = new Array(8).fill(0)

//创建空数组
const arr = new Array()
//等价于
const arr = []
```
### 访问
```js
arr[index]
```
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
