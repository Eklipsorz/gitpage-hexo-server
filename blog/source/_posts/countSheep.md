---
title: CodeWars Learning - Counting sheep
date: 2021-08-09 23:20:27
tags: JavaScript
categories: CodeWars
---

## Description

Consider an array/list of sheep where some sheep may be missing from their place. We need a function that counts the number of sheep present in the array (true means present).


For example,

```
[true,  true,  true,  false,
  true,  true,  true,  true ,
  true,  false, true,  false,
  true,  false, false, true ,
  true,  true,  true,  true ,
  false, false, true,  true]
```

The correct answer would be 17.

Hint: Don't forget to check for bad values like null/undefined


## Solution

先宣告兩個變數(numSheep和realNumSheep)來分別存放陣列的長度以及羊的實際數量，其中
realNumSheep被指派為0，接著再利用for迴圈結構來遍歷陣列的元素尋找true的部分，元素為true時，便讓realNumSheep增加，這個直到系統遍歷完整個陣列，即可獲得羊的真實數量。

```
function countSheeps(arrayOfSheep) {
  // TODO May the force be with you
  let numSheep = arrayOfSheep.length
  let realNumSheep = 0
  for (let index = 0; index < numSheep; index++) {
      if (arrayOfSheep[index] === true)
        realNumSheep++
  }
  
  return realNumSheep
}
```


## Clever Solution

與上個解法不同，利用陣列內建的方法filter\[1\]來從arrayOfSheeps篩選出只有true的內容，其中filter中的Boolean是callback function，系統會從arrayOfSheeps取出每個元素並一個又一個丟入至Boolean function，若該某個元素丟入至function的結果是true的話，那麼就會另外放入新的陣列中，最後當舊陣列的元素都遍歷完之後就會停止，此時filter會回傳一個每個元素都滿足callback function的新陣列-元素全是true的新陣列\[2\]，最後只需要利用length這個內建屬性來獲取新陣列的長度就能獲取羊的實際數量。

```
function countSheeps(arrayOfSheeps) {
  return arrayOfSheeps.filter(Boolean).length;
}
```

\[1\]:filter的使用參數通常會用到callback function，這個function是filter方法篩選的核心條件。

\[2\]:元素全是true的原因：這是由於Boolean這個函式是檢測參數是否為true，一般來說若丟入的參數不是false、null、空字串以及判定false的表達式的話，就會回傳true，否則就為false。


