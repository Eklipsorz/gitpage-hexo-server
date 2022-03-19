---
title: CodeWars Learning - Find the smallest integer in the array
date: 2021-08-11 00:02:59
tags:
 - JavaScript
categories: CodeWars
---
## Description
Given an array of integers your solution should find the smallest integer.

For example:
```
Given [34, 15, 88, 2] your solution will return 2
Given [34, -345, -1, 100] your solution will return -345
```

You can assume, for the purpose of this kata, that the supplied array will not be empty.

## Solution

原本Math物件帶有的min方法可以處理多個參數所組成的List，但現在要處理的對象是陣列，所以得用function物件帶有的apply方法強制將陣列轉換為多個參數所組成的List，也就是Math.min能處理的資料型態。

```
class SmallestIntegerFinder {
  findSmallestInt(args) {
    return Math.min.apply(Math,args)
  }
}
```

另外apply方法本身是允許讓物件呼叫其他物件所擁有的方法，但在這裡並不會用到這項功能，只是純粹讓陣列轉換成min方法可用的型態，所以apply的thisarg部分可填入null來告訴系統沒有讓其他物件去使用Math物件的min方法，另外當替代null時，就允許window物件能夠使用該方法。


另類的寫法：
```
class SmallestIntegerFinder {
  findSmallestInt(args) {
    return Math.min.apply(null,args)
  }
}
```
