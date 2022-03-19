---
title: CodeWars Learning - String repeat
date: 2021-08-10 19:57:10
tags:
 - JavaScript
categories: CodeWars
---

## Description

Write a function called repeatStr which repeats the given string string exactly n times.

```
repeatStr(6, "I") // "IIIIII"
repeatStr(5, "Hello") // "HelloHelloHelloHelloHello"
```


## Solution

解答分為二個版本，第一個版本是未簡化的版本，首先會先判定字串是不是空字串，若是的話，就直接回傳，若不是就透過for迴圈結構以及+這個operator對於字串的串連，最後會因為for迴圈結構而得到好幾個重複的字串串聯在一塊。

```
function repeatStr (n, s) {
  if (s === '') {
    return ''
  } else {
    let newStr = s.slice()
    for (let = n; n > 1; n--) {
        s = s + newStr
    }
    return s
  }
}
```


第二個版本的話，則是透過內建的repeat方法，該方法可以建立多個重複字串來串聯在一起，透過這內建方法以及對於空字串的處理，我們可以寫成這樣：

```
function repeatStr (n, s) {
  return s === '' ? s : s.repeat(n)
}
```

考慮空字串的處理是由於不管有沒有判斷空字串，其結果皆為空字串，並不會有太大的影響，因此可以先判斷來處理，當然也要考慮輸入資料給的空字串是否多到彌補多一層判斷是否空字串的效能損失。

