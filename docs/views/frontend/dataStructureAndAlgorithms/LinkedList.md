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