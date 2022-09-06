---
title: binarySearch
date: 2022-08-29
categories:
  - frond-end
tags :
  - algorithms
---
## 题型1. 获取target
**704**
给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

1. 
```js
var search = function (nums, target) {
  let l = nums.length
  let left = 0
  let right = l - 1
  // 由于right = mid-1, left = mid + 1, 即每次循环后，left或right一定是个新值，最后一次循环left === right时，nums[left]或nums[right]还没有判断过，所以while的条件要写成<=，即当left和right重合时，还是要进行一次判断
  while (left <= right) {
    let mid = left + Math.floor((right - left) / 2)
    if (nums[mid] === target) {
      return mid
    } else if (nums[mid] > target) {
      right = mid - 1
    } else {
      left = mid + 1
    }
  }
  return -1
};
```
2. 
`排除思想`
**把区间分成两个部分，去掉一定不存在目标元素的区间，只在有可能存在目标元素的区间里查找。这样当 left 与 right 重合的时候，我们才可以确定找到了目标元素（或者确定搜索区间里不存在目标元素）。**

**编码要点**
- 循环终止条件写成：while (left < right) ，表示退出循环的时候只剩下一个元素；
- 在循环体内考虑如何缩减待搜索区间，也可以认为是在待搜索区间里排除一定不存在目标元素的区间；
- 根据中间数被分到左边和右边区间，来调整取中间数的行为；
- 如何缩小待搜索区间，一个有效的办法是：从 nums[mid] 满足什么条件的时候一定不是目标元素去考虑，进而考虑 mid 的左边元素和右边元素哪一边可能存在目标元素。
- 一个结论是：当看到 left = mid 的时候，取中间数需要上取整，这一点是为了避免死循环；
- 退出循环的时候，根据题意看是否需要单独判断最后剩下的那个数是不是目标元素。
- 边界设置的两种写法：
- `right = mid` 和 `left = mid + 1` 和 `mid = left + Math.floor(right - left) / 2` 一定是配对出现的；
- `right = mid - 1` 和 `left = mid` 和 `mid = left + Math.ceil(right - left) / 2(int mid = left + (right - left + 1) / 2)` 一定是配对出现的。

```js 
// 条件恒为left<right
// 1. 循环过程中，使left = mid 或 right = mid，即left和right重合时，left === right === mid，一定已经判断过了，可以退出循环。
// 2. 当退出循环时，满足left===right ,结果不必分析是返回left还是right.
// 3. 如果写成<=，mid会出现一直与left/right相等的情况，从而陷入死循环。
while (left < right) { 
  // 可能划分的区间
  // 1.[left,mid-1],[mid,right]
  let mid = left + Math.ceil((right - left) / 2)
  if (nums[mid] > target) {
    right = mid - 1
  } else { // 答案一定是<=mid的值
    left = mid
  }

  // 2.[left,mid],[mid+1,right]
  // 每次修改 left = mid + 1，即一定不会出现left===mid的情况
  let mid = left + Math.floor((right - left) / 2)
  if (nums[mid] >= target) { // 答案一定是>=mid的值
    right = mid
  } else {
    left = mid + 1
  }
}

return nums[left] === target ? left : -1
```
**374**

猜数字游戏的规则如下：
每轮游戏，我都会从 1 到 n 随机选择一个数字。 请你猜选出的是哪个数字。
如果你猜错了，我会告诉你，你猜测的数字比我选出的数字是大了还是小了。
你可以通过调用一个预先定义好的接口 int guess(int num) 来获取猜测结果，返回值一共有 3 种可能的情况（-1，1 或 0）：
-1：我选出的数字比你猜的数字小 pick < num
1：我选出的数字比你猜的数字大 pick > num
0：我选出的数字和你猜的数字一样。恭喜！你猜对了！pick == num
```js
var guessNumber = function(n) {
  let left = 1
  let right = n
  // -1 mid > target   
  while(left<right) {
    let mid = left + Math.floor((right-left)/2)
    let guessRes = guess(mid)
    if(guessRes < = 0) {
      right = mid
    } else {
      left = mid + 1
    }
  }
  return left
};
```
```js
var guessNumber = function(n) {
  let left = 1
  let right = n
  while (left < right) {
    let mid = left + Math.floor((right - left) / 2)
    let guessRes = guess(mid)
    if (guessRes <= 0) {
      right = mid
    } else {
      left = mid + 1
    }
  }
  // 或
  while (left < right) {
  let mid = left + Math.ceil((right - left) / 2)
  let guessRes = guess(mid)
  // mid>target   -1
  if (guessRes < 0) {
    right = mid - 1
  } else {
    left = mid
  }
  }

  return left
};
```
### 题型2 获取第一个>=target的位置
> - 答案一定存在于>=target 的区间内
> - 所以 mid===target可能为答案，mid>target的也可能为答案，mid >= target 的答案要要保证在同一区间，即if(mid>=target) {right= mid}。
> - 如果left = mid,那就是将其中可能的答案划分在一侧区间，>mid的可能答案划分在另一侧区间，边界不清晰，区间划分错乱，答案也必定不正确
> - 所以每次缩小范围，都是right = mid，此时如果采用的是向上取整，可能导致right===mid，如nums = [1,4], target = 3
> - 反过来说，Math.floor是向下取整，也就是说，mid可能与left同值，如果缩小区间采用 left=mid，那么就可能发生left===mid，从而陷入死循环。如nums = [1,3], target = 3

**35 插入搜索位置**
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。请必须使用时间复杂度为 O(log n) 的算法。
```js
var searchInsert = function (nums, target) {
  let l = nums.length
  let left = 0
  let right = l // l可能成为答案 nums = [1], target = 2
  while (left < right) {
    let mid = left + Math.floor((right - left) / 2)
    if (nums[mid] >= target) { 
      right = mid
    } else {
      left = mid + 1
    }
  }
  return left
}
```
```js
var searchInsert = function (nums, target) {
  let l = nums.length
  let left = 0
  let right = l - 1 // 注意 left = mid + 1, 如果right不为l-1， 可能会发生越界

  while (left <= right) {
    let mid = left + Math.floor((right - left) / 2)
    if (nums[mid] === target) {
      return mid
    } else if (nums[mid] > target) {
      right = mid - 1
    } else {
      left = mid + 1
    }
  }
  return left
}
```
**278 第一个错误版本**
假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。
你可以通过调用 bool isBadVersion(version) 接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。
```js
  let left = 1
  let right = n
  while (left < right) {
    let mid = Math.floor((left + right) / 2)
    if (isBadVersion(mid)) {
      right = mid
    } else {
      left = mid + 1
    }
  }
  return left
  // let left = 1, right = n;
  // while (left <= right) { // 循环直至区间左右端点相同
  //   const mid = Math.floor(left + (right - left) / 2); // 防止计算时溢出
  //   if (isBadVersion(mid)) {
  //     right = mid - 1; // 答案在区间 [left, mid-1] 中
  //   } else {
  //     left = mid + 1; // 答案在区间 [mid+1, right] 中
  //   }
  // }
  // return left;
```
### 题型3 获取第一个<=target的位置
> - 答案一定存在于<=target 的区间内
> - 所以 mid===target可能为答案，mid < target的也可能为答案，mid <= target 的答案要保证在同一区间，即if(mid<=target) {left= mid}。
> - 防止发生left === mid 陷入死循环，mid 采用向上取整获取。

**69 x的平方根**
给你一个非负整数 x ，计算并返回 x 的 算术平方根 。
由于返回类型是整数，结果只保留 整数部分 ，小数部分将被 舍去 。
注意：不允许使用任何内置指数函数和算符，例如 pow(x, 0.5) 或者 x ** 0.5 
```js
let left = 0
let right = x
while (left < right) {
  let mid = Math.ceil((left+right)/2)
  if(mid * mid <= x) { 
    left = mid
  }else {
    right = mid-1
  }
}
return left
```











https://suanfa8.com/binary-search/03/