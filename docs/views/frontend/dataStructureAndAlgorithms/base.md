规律：在所有的 for 循环里，判断语句都会比递增语句多执行一次。
https://juejin.cn/book/6844733800300150797/section/6844733800346304526

字符串方法 
- split 方法用于把一个字符串分割成字符串数组
stringObject.split(separator,howmany)

数组方法 
- splice 方法向/从数组中添加/删除项目，然后返回被删除的项目（改变原始数组）
arrayObject.splice(index,howmany,item1,.....,itemX)
- slice 方法可从已有的数组中返回选定的元素（浅拷贝，原始数组不会被改变）
arrayObject.slice(start,end)

### 反转字符串
```js
str.split('').reverse().join('')
```

### 判断一个字符串是否是回文字符串
正着读和倒着读都一样的字符串
（谨记这个对称的特性，非常容易用到）
方法一
```js
function isPalindrome(str){
  var reversedStr = str.split('').reverse().join('')
  return str === reversedStr
}
```
方法二： 利用对称性
（谨记这个对称的特性，非常容易用到）
```js
function isPalindrome(str){
  const len = str.length
  for(let i=0;i<len/2;i++){
    if(str[i]!==str[len-i-1]){
      return false
    }
  }
  return true
}
```

回文字符串的衍生问题

** 如何判断自己解决回文类问题的解法是否“高效”？其中一个很重要的标准，就是看你对回文字符串的对称特性利用得是否彻底。 字符串题干中若有“回文”关键字，那么做题时脑海中一定要冒出两个关键字——对称性 和 双指针。这两个工具一起上，足以解决大部分的回文字符串衍生问题。**

真题描述：给定一个非空字符串 s，最多删除一个字符。判断是否能成为回文字符串。
```js
function validPalindrome(str){
  const len = str.length
  let i = 0
  let j = len-1
  while(i<j && str[i] === s[j]){
    i++
    j--
  }

  if(isPalindrome(i,j-1)){
    return true
  }

  if(isPalindrome(i+1,j)){
    return true
  }

  function isPalindrome(start,end){
    while(start<end){
      if(str[start] !== str[end]){
        return false
      }
      start++
      end--
    }
    return true
  }

  return false
}
```
真题描述： 设计一个支持以下两种操作的数据结构：

void addWord(word)
bool search(word)
search(word) 可以搜索文字或正则表达式字符串，字符串只包含字母 . 或 a-z 。
. 可以表示任何一个字母。

示例:

addWord("bad")
addWord("dad")
addWord("mad")
search("pad") -> false
search("bad") -> true
search(".ad") -> true
search("b..") -> true
说明:
你可以假设所有单词都是由小写字母 a-z 组成的。
