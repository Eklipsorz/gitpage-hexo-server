---
title: CodeWars Learning - Shortest Word
date: 2021-08-10 20:28:32
tags:
 - JavaScript
categories: CodeWars
---
## Description
Simple, given a string of words, return the length of the shortest word(s).

String will never be empty and you do not need to account for different data types.


## Solution

先透過string內建的split方法來把字串轉換為陣列，其元素的取值會以空格當作間隔來取，比如說若字串是"abc efg h"，那麼系統會慢慢讀取字串中的每個字元，當遇到空格時，便會把目前讀取到的字元組合成一個字串來放入新陣列中，也就是abc，接著跳過空格，往下個非空格-e字元讀取，等到讀取到下個空格時，就會組合成新字串-efg放入新陣列，後頭依此類推。


而strArr是根據空格而取出來的新陣列，然後再透過陣列的reduce來將多個元素轉化成單一值-最小字串長度，reduce方法放了arrow function，每個元素會輪流丟進該function中來處理，一開始會從新陣列拿取兩個元素，並且得到它們的長度，誰比較小誰就是下個function的參數a，而參數b則是下個元素，直到新陣列的元素都取完，最後會得到最小長度。

```
function findShort(s){
  
  let strArr = s.split(' ')
  let smallestStr = strArr.reduce((a, b) => a.length >= b.length ? b : a)
  return smallestStr.length

}
```
