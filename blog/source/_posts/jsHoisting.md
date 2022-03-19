---
title: JavaScript - Hoisting簡介
date: 2021-07-28 02:30:23
tags: JavaScript
---


一種預設性的行為，能將所有object和primitive的宣告分別轉移到頂端以優先宣告他們的存在，這功能適用於當使用這些元件時且這些元件的宣告就在之後的程式碼出現，當這個情況出現時便會在使用這些元件的程式碼之前先宣告其元件的存在，而這樣的動作就好像從下方的宣告程式碼搬到到上方來處理，因此得名hoisting，接著宣告完之後便會執行原本的程式碼。 

比如以下程式碼看似有宣告先後的問題:

``` javascript 
console.log (test)
var test = 10
```

但實際上會在處理test/console那段前，先宣告test變數的存在，也就是說實際上會是這樣


```javascript
var test
console.log (test)
var test = 10

```

當然console.log的結果會是undefined，並不會因爲後面的宣告還附加著assignment而跟著更動。


實際上程式碼會是如下所示：

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627411466/Javascript/jsHoisting/hoisitingExample_zbxrqy.png)


## 注意情況
這情況發生在用var宣告的變數或者相關物件，並且並不是真的搬動後頭的宣告程式碼，而是在那些需要他們的程式碼之前先宣告存在這件事，而不包含assignment的動作


## 參考資料

1. https://developer.mozilla.org/zh-TW/docs/Glossary/Hoisting
2. https://www.geeksforgeeks.org/javascript-hoisting/
3. https://blog.techbridge.cc/2018/11/10/javascript-hoisting/ 

