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