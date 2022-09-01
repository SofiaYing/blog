题型1. 获取target
704 
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
排除思想
把区间分成两个部分，去掉一定不存在目标元素的区间，只在有可能存在目标元素的区间里查找。这样当 left 与 right 重合的时候，我们才可以确定找到了目标元素（或者确定搜索区间里不存在目标元素）。
```js 
// 条件恒为left<right
// 1. 循环过程中，使left = mid 或 right = mid，即left和right重合时，left === right === mid，一定已经判断过了，可以退出循环。
// 2. 当退出循环时，满足left===right ,结果不必分析是返回left还是right.
// 3. 如果写成<=，mid可能会出现一直与left/right相等的情况，从而陷入死循环。
while (left < right) { 
  let mid = left + Math.floor((right - left) / 2)
  // 可能划分的区间
  // 1.[left,mid-1],[mid,right]
  // 但是Math.floor是向下取整，也就是说，mid可能与left同值，如果选择方式1，即每次缩小区域，可能会使left=mid,，那么就可能发生left===mid，从而陷入死循环。
  // 如nums = [1,3], target = 3
  if (nums[mid] > target) { 
    right = mid - 1
  } else {
    left = mid
  }

  // 2.[left,mid],[mid+1,right]
  // 每次修改 left = mid + 1，即一定不会出现left===mid的情况
  if (nums[mid] >= target) { 
    right = mid
  } else {
    left = mid + 1
  }
}
return nums[left] === target ? left : -1
```
374 
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
  return left
};
```
题型2 获取第一个>=target的位置
35
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
278 第一个错误版本
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












https://suanfa8.com/binary-search/03/