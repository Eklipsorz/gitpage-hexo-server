---
title: JS - 型別檢查與隱性轉換
tags:
  - type check
categories:
  - JavaScript
date: 2021-09-10 00:33:37
---



型別檢查(Type checking) 會是校驗變數/結構的型別是否符合內容，並回報錯誤或者幫助系統確定變數型別確實是如此，主要是盡可能偵測相關問題並及時給予機會去修正。

## 型別檢查什麼時期執行

型別檢查會發生在編譯期間或者執行期間，若是在編譯期間進行檢查的話，代表所有的程式碼都必須在執行前或者更早之前指定型別給會用到的變數和內容，而開發者可以選擇在開發期間指定型別給每個變數和內容，並在編譯期間讓編譯器去做型別檢查做第二輪的檢查，或者也可以不用開發者去指定，讓編譯期間的編譯器根據內容去做檢查，通常會選擇前者，比較能夠透過額外的資訊和第一輪檢查(開發者)增加型別的正確性。

而若是在執行期間進行檢查的話，代表所有程式碼都是在執行時根據指派內容和一些指派規則來確定變數和內容各是什麼樣的型別，當然地，型別確定可以不限於第一次的型別檢查，還有可能會是根據每次指派內容而變動相同變數的型別，比如說變數a第一次做型別檢查時，是被判定為數字型別，而接著a儲存字串資料，而這時會做二次型別檢查，使變數a轉變為字串型別。


## 靜態語言與動態語言
若某程式語言是在編譯期間進行型別檢查的話，該程式語言會是靜態語言(Statically Typed Languages)；若某程式語言是在執行期間進行的話，該程式語言會是動態語言(Dynamically typed languages)。


## JavaScript 是屬於？

JavaScript由於本身是透過直譯器來執行，無法在編譯時期執行型別檢查，只能選擇在執行期間進行型別檢查以及確定型別，因此它屬於動態語言，JavaScript會根據 "每一次" 的指派內容來指定 “每一次" 的型別檢查和確定型別，這也就是一個變數型別可以根據內容而轉換為JavaScript任意可辨識的型別，使得開發者變得更好開發，但對於結果來說，會容易產生出預期以外結果，比如說由表達式所構成的一個值或者一個物件，該表達式可能有不少的變數、常數、運算符號，其形式會是：var1至varN是變數，constant1至constantN是常數，operator1至operator(N-1)是運算符號，這些參數會構成足以代表一個值或者一個物件的表達式，並將代表的內容傳進value1這變數

```
let value1 = var1/constant1 operator1 var2/constant2 ...... operator(N-1) varN/constantN
```

若這些變數和常數的型別皆為一樣時，我們很容易預估最後結果的型別以及value1變數型別是什麼，但是如果某些變數或者常數的型別是不太一樣時，這時我們就很難預估型別是什麼，得依賴著直譯器對於他們的判定。


### 型別轉換(Type Conversion)

直譯器遇到這些不同型別的變數或者常數時，你可以選擇添加一些轉換型別的語法來為這些變數和常數進行顯性轉換(Type Casting)，但若不添加的話，就是由直譯器負責處理隱性轉換(Type Coercion，不透過較明顯的轉換語法)，而隱性轉換會根據直譯器內部的轉換規則來進行，比如說它會從表達式抽出最優先計算的一部分表達式，通常會是

```
var1/constant1 operator1 var2/constant2
```

並且檢查var1/constant1 和var2/constant2哪一邊是字串，若有字串，整條表達式會以字串形式來進行並以字串來輸出，接著就是按照operator的性質來決定非字串的資料要如何隱性轉換。


#### 已知的隱性轉換規則

加法比較特別一點，它會試著將用到的參數轉換成字串來串連或者以數值系統的加法來處理，目前已知的轉換規則是：
1. 當兩邊皆能夠用數字表示時，比如false、null，會用數值系統的加法
2. 若其中一邊為物件時，會用字串形式來串連

若是operator是能處理數字的加減乘除的話，那麼就是按照數字型別來隱性轉換，比如：

1. 100 + false 就會是

```
100 + Number(false) = 100 + 0 = 100
```

2.  99 + null + 1 就會是

```
99 + Number(null) + 1  = 99 + 0 + 1 = 100
```

3. 99 + true + NaN 就會是

```
99 + Number(true) + Number(NaN)  = 99 + 1 + NaN = NaN
```

4. 99 + true + undefined 就會是，其中Number的參數若不是正常數字時就會是NaN

```
99 + Number(true) + Number(undefined)  = 99 + 1 + NaN = NaN
```




### 避免隱性轉換的開發建議

1. 使用顯性轉換
2. 要比較內容時，請使用====或者!==，別使用==或者!=，這會使直譯器繼續使用隱性轉換


## 補充資料：

1. 型別轉換(Type conversion)可以是顯性或者隱性轉換，若是顯性轉換的話，會透過較明顯的轉換語法來達到型別轉換，直接由開發者自行決行轉換什麼型別，比如：

```
console.log(100 + Number(false))          // 100 + 0 = 100
```
而若是隱性轉換的話，就如同前面所述，不會透過明顯語法來達到目標，而是由直譯器來決定型別是什麼，比如：
```
console.log(100 + false)                  // 100 + 0 = 100
```


2. 可以透過顯性轉換轉換為true/false、數字0/1：
a. 可在boolean系統轉換為true的內容，有非空內容的字串(包括'0')、非0的數字、非Null物件。
b. 可在boolean系統轉換為false的內容，有空字串、0、NaN、null、undefined、false。 
c. 可轉換數字的內容，有具有數字的字串、boolean值、空字串

3. 當if/else條件式、while、for等具有boolean條件式(用boolean值的true和false判定條件成立)使用“原本可在顯性轉換中能轉換為true/flase”的資料作為條件式且只有單一值/單一物件時，其直譯器會以隱性轉換將這些資料轉換，其結果會如同使用顯性轉換下的結果。比如說：設定testVar為NaN，當進入if的條件式中，直譯器就會以Boolean(testVar)來轉換，最後會獲得false進入else來印I'm false。

```
let testVar = NaN
if (testVar) {
    console.log('I\'m true')
} else {
    console.log('I'\m false')
}
```

## 參考資料
1. https://developer.mozilla.org/en-US/docs/Glossary/Type_coercion
2. https://stackoverflow.com/questions/8857763/what-is-the-difference-between-casting-and-coercing
