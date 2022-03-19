---
title: CodeWars Learning - Alan Partridge II - Apple Turnover
date: 2021-08-11 16:06:27
tags:
 - JavaScript
categories: CodeWars
---
## Description
Your job is simple, if (x) squared is more than 1000, return 'It's hotter than the sun!!', else, return 'Help yourself to a honeycomb Yorkie for the glovebox.'.

## Solution

考慮著輸入參數x是由字串所構成的數字，類似這兩種'-1'或者'2'，所以得在做判定之前，先透過+這個符號將輸入參數x強制轉換成數字型態，而+x相等於以下：
```
Number(x) * 1 // * 1 是表示強制轉型後的符號，若是-x，則為 * (-1)
```
轉換為數字之後就能計算平方後是否大於1000來做之後的判斷。

```
function apple(x){
  return Math.pow(+x, 2) > 1000 ? 'It\'s hotter than the sun!!' : 'Help yourself to a honeycomb Yorkie for the glovebox.'
}

```
