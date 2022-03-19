---
title: CodeWars Learning - Geometry Basics &#58; Distance between points in 2D
date: 2021-08-11 16:20:31
tags:
 - JavaScript
categories: CodeWars
---
## Description
This series of katas will introduce you to basics of doing geometry with computers.

Point objects have x and y attributes (X and Y in C#) attributes.

Write a function calculating distance between Point a and Point b.

Tests round answers to 6 decimal places.

## Solution

由於傳過來的輸入參數a和b皆為擁有x和y這兩個屬性的物件，所以只需要計算x座標軸的值和y座標軸的值，最後再透過兩點的距離公式來獲取兩點的距離。

```
function distanceBetweenPoints(a, b) {

  let xAxis = Math.abs(a.x - b.x)
  let yAxis = Math.abs(a.y - b.y)
  
  return Math.sqrt(Math.pow(xAxis, 2) + Math.pow(yAxis, 2))

}
```
另一個版本的答案則是使用Math物件的內建方法hypot，以a點和b點來構成三角形並求得這三角形的斜邊長度(hypotenuse)，而這個長度剛好是這兩點的距離。

```
function distanceBetweenPoints(a, b) {
  return Math.hypot(a.x - b.x, a.y - b.y)
}

```


