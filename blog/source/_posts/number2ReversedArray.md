---
title: CodeWars Learning - Convert number to reversed array of digits
date: 2021-08-10 20:40:56
tags:
 - JavaScript
categories: CodeWars
---
## Description

Convert number to reversed array of digits

Given a random non-negative number, you have to return the digits of this number within an array in reverse order.

Example:
```
348597 => [7,9,5,8,4,3]
```
## Solution

由於字串在JavaScript當中可被當作array like 物件來處理，當系統這樣判定的時候，字串中的每個字元會被當作獨立的元素來處理，接著每個元素會被丟入至arrow function
來將元素轉換成數字(如下所示)：

```
(Array.from(String(n), input => Number(input)))
input => Number(input) // It's a arrow function
```
比如說當字串為1234567，那麼系統會看作為1、2、3.....、7這7個字元元素，然後每個元素丟入至Number(input)來轉換成數字，最後會獲得1、2、3、....、7這7個數字元素。

轉換完真正的數字陣列之後，就直接透過內建的reverse方法來將陣列元素反轉就會是答案。

```
function digitize(n) {
  return (Array.from(String(n), input => Number(input))).reverse()
}
```

