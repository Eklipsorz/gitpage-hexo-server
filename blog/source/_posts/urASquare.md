---
title: CodeWars Learning - You're a square!
date: 2021-08-10 21:50:20
tags:
 - JavaScript
categories: CodeWars
---
## Description

You like building blocks. You especially like building blocks that are squares. And what you even like more, is to arrange them into a square of square building blocks!

However, sometimes, you can't arrange them into a square. Instead, you end up with an ordinary rectangle! Those blasted things! If you just had a way to know, whether you're currently working in vain… Wait! That's it! You just have to check if your number of building blocks is a perfect square.

Task:

Given an integral number, determine if it's a square number:

In mathematics, a square number or perfect square is an integer that is the square of an integer; in other words, it is the product of some integer with itself.

The tests will always use some integral number, so don't worry about that in dynamic typed languages.

Examples:

```
-1  =>  false
 0  =>  true
 3  =>  false
 4  =>  true
25  =>  true
26  =>  false
```
## Solution

考慮輸入值會是負值，所以設定一個條件式來判定若輸入為負值，就直接回傳false，而若不是的話，就緊接透過下式來判定輸入是否特定數的平方數。

```
Math.sqrt(n) % 1 === 0
```

若n為特定數的平方數，開根號後肯定為整數，而若不是的話，會帶有小數點，為了再進一步區分，利用%1這運算來判定該數是否真是平方數。

```
var isSquare = function(n){
    return (n >= 0) ? Math.sqrt(n) % 1 === 0 : false
}
```

另外由於sqrt碰上負值皆會直接回傳NaN且NaN不跟任何數字(含自己)相等，所以以我們還可以進一步減少 (n >= 0) 和其附帶的false來換取更好的效率。

```
var isSquare = function(n){
    return Math.sqrt(n) % 1 === 0
}
```


