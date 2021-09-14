---
title: Tree
date: 2020-12-24
categories:
  - frond-end
tags :
  - dataStructure
---
### 创建
```js
function TreeNode(val){
  this.val = val
  this.left = this.right = null
}

const node = new TreeNode(1)
```
## 递归
- 递归式：每一次重复的内容
- 递归边界： 什么时候停下来

最大的嵌套调用次数（包括首次）被称为 递归深度。

最大递归深度受限于 JavaScript 引擎。对我们来说，引擎在最大迭代深度为 10000 及以下时是可靠的，有些引擎可能允许更大的最大深度，但是对于大多数引擎来说，100000 可能就超出限制了。有一些自动优化能够帮助减轻这种情况（尾部调用优化），但目前它们还没有被完全支持，只能用于简单场景。

这就限制了递归的应用，但是递归仍然被广泛使用。有很多任务中，递归思维方式会使代码更简单，更容易维护。

**任何递归都可以用循环来重写。通常循环变体更有效。**
……但有时重写很难，尤其是函数根据条件使用不同的子调用，然后合并它们的结果，或者分支比较复杂时。而且有些优化可能没有必要，完全不值得。

递归可以使代码更短，更易于理解和维护。并不是每个地方都需要优化，大多数时候我们需要一个好代码，这就是为什么要使用它
### 二叉树遍历
先序遍历
递归式：根节点-左子树-右子树
```js
function preorder(root){
  if(!root){ return }
  
  console.log('打印当前节点',root.val)
  preorder(root.left)
  preorder(root.right)
}
```
中序遍历
```js
function inorder(root){
  if(!root){ return } 

  inorder(root.left)
  console.log(root.val)
  inorder(root.right)
}
```
后序遍历
```js
function postorder(root){
  if(!root){ return }

  postorder(root.left)
  postorder(root.right)
  console.log(root.val)
}
```
层次遍历
```js
function level(root){
  if(!root){return}
  const res = []
  const queen = []
  queen.push(root)

  while(queen.length>0){
    let node = queen.shift()
    res.push(node.val)
    if(node.left){
      queen.push(root.left)
    }
    if(node.right){
      queen.push(root.right)
    }
  }
}

function level(root){
  if(!root){return}
  const res = []
  const queen = []
  queen.push(root)
}
```