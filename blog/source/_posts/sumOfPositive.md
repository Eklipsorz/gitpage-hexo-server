---
title: CodeWars Learning - Sum of positive
date: 2021-08-10 19:51:27
tags:
 - JavaScript
categories: CodeWars
---

## Description
You get an array of numbers, return the sum of all of the positives ones.

Example [1,-4,7,12] => 1 + 7 + 12 = 20

Note: if there is nothing to sum, the sum is default to 0.


## Solution

先使用陣列的內建方法-filter來篩選出大於0的元素並放入新陣列中，接著利用reduce方法將多個元素轉換成單一值，也就是所有元素的總和

```
function positiveSum(arr) {
  let newArr = arr.filter((val)=> val > 0)
  return newArr.reduce((currVal, addedVal)=> currVal + addedVal, 0)
}
```
