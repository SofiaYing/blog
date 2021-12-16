---
title: Array
date: 2020-12-24
categories:
  - frond-end
tags :
  - dataStructure
---

在 JavaScript 中有 **8 种基本的数据类型**（7 种原始类型和 1 种引用类型）
我们可以将任何类型的值存入变量。例如，一个变量可以在前一刻是个字符串，下一刻就存储一个数字：
```js
// 没有错误
let message = "hello";
message = 123456;
```
允许这种操作的编程语言，例如 JavaScript，被称为 **“动态类型”（dynamically typed）**的编程语言，意思是虽然编程语言中有不同的数据类型，但是你定义的变量并不会在定义后，被限制为某一数据类型。

- Number 用于任何类型的数字：整数或浮点数，在 ±(253-1) 范围内的整数。
- Bigint 用于任意长度的整数。
- String 用于字符串：一个字符串可以包含 0 个或多个字符，所以没有单独的单字符类型。
- Boolean 用于 true 和 false。
- null 用于未知的值 —— 只有一个 null 值的独立类型。
- undefined 用于未定义的值 —— 只有一个 undefined 值的独立类型。
- Symbol 用于唯一的标识符。
- Object 用于更复杂的数据结构