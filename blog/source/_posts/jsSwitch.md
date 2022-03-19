---
title: JavaScript - Switch 使用方式
date: 2021-07-25 23:18:24
tags: JavaScript
mathjax: true
---



根據switch包含的表達式(expression)最後得出的結果(result)來挑選以下case來做處理，其挑選方式類似於Goto和label，但不一定真是使用這兩個語法：每個case會類似於label那樣，只是告訴系統要跳到哪裡，而挑選這些case的處理會告訴系統如何透過對應的結果和類似goto的語法來跳躍至指定的case。


下面是switch的基本語法架構，switch括號內部是存放對應結果的表達式，而下方的case則是對於這個結果的預期結果，只要對應到的話，就會執行該結果內包含的處理。


```javascript
switch (expression 1) {
    case result 1:
        statement/expression
        break
    case result 2:
        statement/expression
        break
        .
        .
        .
        .
        
    case result N:
        statement/expression
        break
}
```

case旁的result 1至result N會是表達式1的可能結果且每個result的data type會和表達式的結果之data type呈現一樣的關係。當表達式1的結果是result 1，系統就會跳入至以下程式碼來做處理。


```
 case result 1:
        statement/expression
        break
```

而如果表達式1的結果會是result 3，系統就會跳入至case result 3的部分：

```
 case result 3:
        statement/expression
        break
```




### default:
透過default來預設所有case都滿足不了的情況並按照default內容來處理，其語法會是下圖那樣：
```
default: 
    statement/expression
    break
```



```
switch (expression 1) {
    case result 1:
        statement/expression
        break
    case result 2:
        statement/expression
        break
        .
        .
        .
        .
        
    case result N:
        statement/expression
        break
}
```



### 需要注意的事情：

1. 每個case都會用break來阻止系統往下執行。若沒有break，當執行完某個case所指定的處理後，會直接往下執行其他case的處理內容，直到遇到break或者執行完。
2. 當出現多個相同result的case時，會挑最前面的case來執行。
3. 若把default放在所有case之前的話，並不會讓系統在滿足某case的情況下而跳轉default，而是優先跳入對應該case的處理。



## 參考資料：
1. https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/switch
