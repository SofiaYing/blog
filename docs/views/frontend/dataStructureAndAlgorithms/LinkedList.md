---
title: Linked-list
date: 2020-12-24
categories:
  - frond-end
tags :
  - dataStructure
---
### 创建
```js
function ListNode(val){
  this.val = val
  this.next = null
}

const node = new ListNode(1)
node.next = new ListNode(2)
```
### 插入节点
```js
const node3 = new ListNode(3)
node3.next = node1.next
node1.next = node3
```
### 删除元素
```js
node1.next = node3.next  //node3不可达，会被java垃圾回收机制回收
// node3.next = null
```
### 数组VS链表
1. 在大多数计算机语言里，数组都对应着一段连续的内存，我们假设数组的长度是 n，那么因增加/删除操作导致需要移动的元素数量，就会随着数组长度 n 的增大而增大，呈一个线性关系。所以说数组增加/删除操作对应的复杂度就是 O(n)。
2. 但 JS 中不一定是。如果我们在一个数组中只定义了一种类型的元素，比如：const arr = [1,2,3,4]
它是一个纯数字数组，那么对应的确实是连续内存。
但如果我们定义了不同类型的元素：const arr = ['haha', 1, {a:1}]
它对应的就是一段非连续的内存。此时，JS 数组不再具有数组的特征，其底层使用哈希映射分配内存空间，是由对象链表来实现的
3. 链表有一个明显的优点，就是添加和删除元素都不需要挪动多余的元素，复杂度为O(1)。
4. 链表访问操作复杂，需要进行遍历，复杂度为 O(n),数组直接访问索引复杂度为O(1)
```js
//查找第10个链表元素
const index = 10
let node = head
for(let i=0;i<index && node;i++){
  node = node.next
}
```
真题描述：将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有结点组成的
```js
function concat(l1,l2){
  let resList = new nodeList();
  let cur = resList
  while(l1 && l2){
    if(l1.val < l2.val){
      cur.next = l1
      l1 = l1.next
    }else{
      cur.next = l2
      l2 = l2.next
    }
    cur = cur.next
  }
  // 处理链表不等长的情况
  cur.next = l1!==null?l1:l2
  return head.next
}
```
真题描述：给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。
```js
function delDuplicates(list){
  let cur = list

  while(cur !== null && cur.next !== null){
    if(cur.next.val === cur.val){
      cur.next = cur.next.next
    }else{
      cur = cur.next
    }
  }  

  return list
}
```
dummy结点： 处理掉头结点为空的边界问题
其实在链表题中，经常会遇到这样的问题：链表的第一个结点，因为没有前驱结点，导致我们面对它无从下手。这时我们就可以用一个 dummy 结点来解决这个问题。
所谓 dummy 结点，就是咱们人为制造出来的第一个结点的前驱结点，这样链表中所有的结点都能确保有一个前驱结点，也就都能够用同样的逻辑来处理了。

真题描述：给定一个排序链表，删除所有含有重复数字的结点，只保留原始链表中 没有重复出现的数字。
```js
function delPublicates(list){
  let dummy = new ListNode()
  dummy.next = list
  let cur = dummy 
  while(cur.next && cur.next.next){
    if(cur.next.val === cur.next.next.val){
      let val = cur.next.val
      while(cur.next && cur.next.next===val){
        cur.next = cur.next.next
      }
    }else{
      cur = cur.next
    }
  }
}
```

快慢指针与多指针
链表题目中，有一类会涉及到反复的遍历。涉及反复遍历的题目，题目本身虽然不会直接跟你说“你好，我是一道需要反复遍历的题目”，但只要你尝试用常规的思路分析它，你会发现它一定涉及反复遍历；同时，涉及反复遍历的题目，还有一个更明显的特征，就是它们往往会涉及相对复杂的链表操作，比如反转、指定位置的删除等等。

解决这类问题，我们用到的是双指针中的“快慢指针”。快慢指针指的是两个一前一后的指针，两个指针往同一个方向走，只是一个快一个慢。快慢指针严格来说只能有俩，不过实际做题中，可能会出现一前、一中、一后的三个指针，这种超过两个指针的解题方法也叫“多指针法”。

快慢指针+多指针，双管齐下，可以帮助我们解决链表中的大部分复杂操作问题。



dummy 结点：它可以帮我们处理掉头结点为空的边界问题，帮助我们简化解题过程。因此涉及链表操作、尤其是涉及结点删除的题目（对前驱结点的存在性要求比较高），我都建议大家写代码的时候直接把 dummy 给用起来，建立好的编程习惯
```js
const dummy = new ListNode()
dummy.next = head
```

真题描述：给定一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

```js
function delDuplicates(list,n){
  let dummy = new NodeList()
  dummy.next = list
  let slow = dummy.next
  let fast = dummy.next
  let count = 1

  while(fast && fast.next){  
    if(count<n){
      fast = fast.next
    }else{
      slow = slow.next
    }
    count++
  }

  return count-1
}


```
