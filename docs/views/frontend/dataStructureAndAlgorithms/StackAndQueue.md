---
title: StackAndQueue
date: 2020-12-24
categories:
  - frond-end
tags :
  - dataStructure
---
## Stack: 后进先出LIFO(Last In First Out)
可理解为只允许push pop的特殊数组，通常还有读取栈顶元素的需求
```js
const stack = []
stack.push(1)
stack.push(2)

while(stack.length){
  const top = stack[stack.length-1]
  stack.pop()
}
//stack []
```
## Queue: 先进先出FIFO(First In First Out)
可理解为只允许shift push的特殊数组
```js
const queue = []
queue.push(1)
queue.push(2)

while(queue.length){
  const top = queue[0]
  queue.shift()
}
//queue []
```
