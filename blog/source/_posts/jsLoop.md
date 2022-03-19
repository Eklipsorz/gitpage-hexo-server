---
title: JavaScript - Loop 結構
date: 2021-07-26 00:34:06
tags: JavaScript
---



該結構是允許程式碼基於布林值條件式是否成立而給予適當次數的重複處理或者重複執行特定的statement，該結構會由for或while而構成。

## for 語法
會搭配著變數、判定該變數的條件式、做完一次的後續處理，接著再跳回該變數的條件式來判定是否結束或者繼續做相同的事情。
    
當系統進入for的開頭時(如下語法)，會先執行assignment，然後再處理condition的部分，若為true時，便會進入for結構的內部，也就是執行statement 1，若為false就跳到for結構之外的statement 2，當結果是true並處理完statement 1之後，便會跳到for開頭中的statement 0 繼續後續處理，然後再進入condition來判定是否為true，直到condition的處理結果為false才會終結這個for-loop的重複執行。

```javascript
for (assigment; condition; statement 0){
    
	statement 1
  
}
    
statement 2

```

以流程圖來說，會像是這樣：
    

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627237717/Javascript/jsLoop/forFlowChart_k5svnj.png)

其中設定變數x為assignment之內容，然後條件式就是“判定x是否滿足條件式”，若不滿足則會跳出去，若滿足則會處理內部的statement，接著處理後續的statement 0，並重回條件式繼續判定是否true或者false。

## while 語法
與for語法相比，只會搭配布林條件式，並且少了處理設定初始值和statement 0這兩個步驟，語法上會是
    
```javascript
while (condition) {
    
   statement 1
    
}
    
statement 2
```
    
系統讀到while這行時便會判斷condition是否得出true，若是true的話，則會跳入statement 1，並且執行完回到condition繼續判斷直到false為止，並且跳到statement 2執行。如果一開始的condition判斷為false，那麼就會跳到statement 2去執行。
    
過程會是：
    
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627237717/Javascript/jsLoop/whileFlowChart_qra3lb.png)




## Break - 中斷loop所產生的重複性處理

當我們想在loop中剛好滿足一個條件並且想中斷後續的重複性處理，那麼我們可以利用break來達到目的，其語法上只需要放入想要臨時中斷的loop結構中，比如像是以下的for和while這兩個語法：


```
for () {
    
	break
    
}
    
while () {
        
	break
    
}
```

當系統讀取break時，不論後續的重複性處理是否存在，皆會中斷當前離自己最近的loop結構。但如果說存在著巢狀loop的話，那麼break只會發揮一次性的中斷，而且目標會是最近的loop結構，比如說：

```
    for () {
        for {
        
            break
        }
    
    }

```

break會中斷最內部的loop結構。



## Continue - 直接跳到下一個迭代

當我們想直接跳到下一個判斷式＋重複處理且不想處理當前loop的後續觸法時，我們可以使用continue，比如說：


```
    for (assignment; condition; statement 0) {
        
        statement 1
        continue
        statement 2
    
    
    }

    statement 3
```

這段程式碼處理完statement 1之後，讀取到continue會直接跳過statement 2並且跳回至for中的後續處理以及條件式。其流程圖如下：


未存在continue時，會像是這樣的流程：
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627237717/Javascript/jsLoop/noContinueFlowChart_wrww67.png)


存在continue時，就會像是這樣的流程：

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627237717/Javascript/jsLoop/continueFlowChart_m9p7ss.png)




