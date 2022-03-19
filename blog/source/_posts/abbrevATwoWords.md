---
title: CodeWars Learning - Abbreviate a Two Word Name
date: 2021-08-10 21:12:53
tags:
 - JavaScript
categories: CodeWars
---
## Description
Write a function to convert a name into initials. This kata strictly takes two words with one space in between them.

The output should be two capital letters with a dot separating them.

It should look like this:

```
Sam Harris => S.H

Patrick Feeney => P.F
```

## Solution


由於每個字之間會有空格，所以可以使用split來將name轉換成陣列，接著呼叫陣列的內建方法map，將陣列中的每個元素重新轉換成原本首字大寫，其中map內容會夾雜著callback function，而系統會把每個元素丟入至這個function來取得每個字串的第一個字元，接著將其轉換為大寫，最後結果為轉換後的陣列。

```
str => str.charAt(0).toUpperCase()
```

取得轉換後的陣列，再透過toString方法將陣列轉換成字元陣列，而這個字元陣列的字元之間會由於toString本身的功能而出現逗號，所以得呼叫replace將逗號替代成'.'符號。

另外replace的替代只會發生在它第一次找到符合條件的字元內容，之後符合的內容就不會替代。

```
function abbrevName(name){

  let newName = name.split(' ').map(str => str.charAt(0).toUpperCase())
  return newName.toString().replace(',','.')
  
}

````



