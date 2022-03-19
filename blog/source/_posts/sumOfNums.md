---
title: CodeWars Learning - Sum of Numbers
date: 2021-08-10 00:31:55
tags: 
 - JavaScript 
 - Math
categories: CodeWars
---

## Description

Given two integers a and b, which can be positive or negative, find the sum of all the integers between and including them and return it. If the two numbers are equal return a or b.

Note: a and b are not ordered!

Examples:
```
GetSum(1, 0) == 1   // 1 + 0 = 1
GetSum(1, 2) == 3   // 1 + 2 = 3
GetSum(0, 1) == 1   // 0 + 1 = 1
GetSum(1, 1) == 1   // 1 Since both are same
GetSum(-1, 0) == -1 // -1 + 0 = -1
GetSum(-1, 2) == 2  // -1 + 0 + 1 + 2 = 2
```

## Solution
由於這題給予的數字是連續性且要求這些數字的總和，可利用1+2+...+N的高斯加法來求解，而這個加法概念會是建立另一個相同且連續性的數列，然後數列中的每一個數字之順序是顛倒，顛倒的數列和沒顛倒的數列相加會變成：

```
	第一個數列： 1 + 2 + 3 + 4 + ....... + N
	第二個數列： N + N-1 + N-2 + N-3 + ..... + 1
	相加後： (N+1) + (N+1) ........ + (N+1)
```

會發現其結果為N個(N+1)的相加結果，在這個時候只要對它們除以2的話，就會是原本的1+2+....+N的總和，在這裡我們可以將常數部分更改為變數型態(我們以M為主)，你會發現最後會是N個(2M+N)之總和，只要我們再除以2就會是M + M+1 + .... + M+N的總和
```
	第一個數列： M + M+1 + M+2 + M+3 + ....... + M+N
	第二個數列： M+N + M+N-1 + M+N-2 + M+N-3 + ...+ M+1 + M
	相加後： (2M+N) + (2M+N) + ........ + (2M+N)
```

根據這些概念，我們只需要從參數中找到誰才是數列的起始值以及數列的尾值，找到並且去計算該數列的長度和(尾值+起始值)之總和，最後再利用以上相同的方法，去建立相同的數列相加，然後再除以2就能獲得答案。

```
function getSum( a,b )
{
  
  let start = a > b ? b : a
  let end = a > b ? a : b
  
  return (((end - start) + 1) * (start + end))/2
   
}

```

