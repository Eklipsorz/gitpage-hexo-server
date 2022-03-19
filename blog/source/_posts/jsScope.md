---
title: JavaScript - Scope 簡介
tags:
  - JavaScript
  - Scope
date: 2021-09-09 17:47:19
---


在程式語言中，作用域(Scope)是指對應某種實體(entity)的名字(name)所能夠被合法辨識以及使用的範圍，其中實體是指的是某種記憶體區塊，而名字就是變數(variable)名稱，換言之，只要我們透過變數名稱就能操控代表記憶體區塊的實體。在這裡皆以let、const這些不違反Scope規則的變數宣告方式，var宣告會另開一個小節做說明


## Scope 有哪些種類？

Scope可以是由{}所構成的範圍以及未被{}所構成的範圍，他們的形式通常分別會是如下：首先是由{}所構成的範圍，這個範圍又被稱之為區塊(Block)，其構成必須藉由開發者自行設定才會產生

```
{
    statement1 / expression 1
               .
               .
               .
}
```
另一個則是不用括號

```
statement1 / expression 1
           .
           .
           .
           .

```

預設上若沒特定添加{}的話，變數所在的作用域會是在不用括號的範圍，在這個範疇中會是全域作用域(Global Scope)，而括號構成的範圍只會出現全域作用域內部，對全域作用域而言，該範圍會是區域作用域(Local Scope)，其中根據全域作用域內包含區域作用域，我們可以將全域作用域成這個區域作用域的Parent Scope，而這區域作用域會是全域作用域的Child Scope，當然我們也可以在區域作用域內建立另一個區塊，形成另一種Parent-Child之間的作用域，若再進一步區分的話其中最根源或者包含所有區域作用域的作用域會是Root Scope，而這個作用域正是全域作用域。

現在我們知道預設上我們會在Root Scope進行宣告以及定義變數，在這裡所宣告的變數所擁有的作用域會是Root Scope或 Global Scope，而此變數會被稱之為全域變數，若跳脫Scope的範圍或者執行完畢時，其變數所佔用的記憶體會被釋放，若是在Root Scope內部產生另一個Scope並進行變數宣告的話，其額外產生的Scope對於Global Scope而言會是Local Scope，在那裡宣告的變數所擁有的Scope會只有那區塊，而不是Root Scope，而且該變數只要跳脫那Scope，它所佔用的記憶體空間會被釋放掉。


比如首先我們先替Root Scope取名為Scope A，其內部再產生一個名為Scope B的Scope，括號內部又宣告了一個變數b，其變數b的Scope只有括號內部而已。

```
// Scope A: Root Scope
let a = 10

{ // Scope B: Child Scope of Root Scope

    let b = 20

}

```

在這情況下的變數b會被稱作為區域變數，且該Scope A對於由括號構成的Scope B而言，Scope A會是他的Parent Scope，而Scope B會是Scope A的Child Scope。若我們繼續沿用上面例子中的Scope B內產生另一個名為Scope C的Scope的話，也就是如下所示：

```
// Scope A: Root Scope
let a = 10

{ // Scope B: Child Scope of Root Scope
    let b = 20 
    { // Scope C: Child Scope of Scope B
        let c = 30
    }
    

}

```

其Scope C會是Scope B的Parent Scope，而Scope C則會是Scope B的Child Scope。

## Root、Parent、Child這三者下的變數存取關係

若我們把這些括號給去除掉的話，實際上還是同一個作用域下的變數，後面變數可以存取前面已宣告的變數值，只是現在我們只是單純按照括號賦予他們特定的作用域，進而告訴變數什麼時候該自己釋放記憶體，否則若不釋放會因為被判定成不屬於它原本的作用域而出錯。在這樣規則下，我們可以得知二件事：1. 變數宣告的先後順序仍沒改變，後面變數可以存取前面已宣告的變數，2. 變數所在的作用域會影響著它們何時釋放，接著我們將利用這兩套被推斷的規則以及例子來得知Root、Parent、Child這三者下的變數存取關係是什麼。

```
// Scope A: Root Scope
let a = 10

 // Scope B: Child Scope of Root Scope
    let b = 20 
     // Scope C: Child Scope of Scope B
        let c = 30
    
```

在這小節中，會以三個小例子來說明存取關係是如何，而這些例子分別可以推斷出

1. 無法在Parent Scope存取Child Scope所定義的變數。
2. Child Scope 能夠存取位於Parent Scope的變數，但前提必須看要存取的變數是否在Child Scope之前。
3. 多個Parent Scope是相同的Child Scope是無法存取彼此間的變數。

### 例子：無法在Parent Scope存取Child Scope所定義的變數。

繼續沿用上個例子，當想在Root Scope去印出Scope C下的變數或者Scope B的變數時，在只能待在Root Scope的前提下，只能有兩種選擇方式：a. 在Scope B前寫印變數的程式、b. 在Scope B後寫出印變數的程式

```
// Scope A: Root Scope
let a = 10

{ // Scope B: Child Scope of Root Scope
    let b = 20 
    { // Scope C: Child Scope of Scope B
        let c = 30
    }
    

}

```

這兩種選擇方式分別如下所示：

a. 在Scope B前寫印變數的程式：

```
// Scope A: Root Scope
let a = 10

console.log(b)
console.log(c)
{ // Scope B: Child Scope of Root Scope
    let b = 20 
    { // Scope C: Child Scope of Scope B
        let c = 30
    }
    

}

```

b. 在Scope B後寫出印變數的程式：


```
// Scope A: Root Scope
let a = 10


{ // Scope B: Child Scope of Root Scope
    let b = 20 
    { // Scope C: Child Scope of Scope B
        let c = 30
    }
    

}
console.log(b)
console.log(c)
```

但這兩種方式皆無法正常印出變數b和變數c，a方式是因為變數b和變數c都還沒被宣告定義，所以本來就印不出來，而b方式則是因爲Scope內的所有變數的記憶體皆被釋放，所以也就跟著印不出來，同樣的概念也可以放在只考慮Scope B和 Scope C這兩者上，將他們兩者換成Parent Scope和Child Scope，當然最後結果會是沒辦法在Parent Scope存取到Child Scope下的變數。



### 例子2: Child Scope 能夠存取位於Parent Scope的變數

同樣地，若我們想要在Child Scope去存取Parent Scope下的變數，比如想在Scope B的範圍下印出Parent Scope的變數a，這時我們會因為Parent Scope的變數a還存在而能夠印出來。

```
// Scope A: Root Scope
let a = 10


{ // Scope B: Child Scope of Root Scope
    let b = 20  
    console.log(a)

    { // Scope C: Child Scope of Scope B
        let c = 30
    }
    

}

```

但變數a的宣告定義是放在Scope B後頭的話，也就是像這樣，
```
// Scope A: Root Scope



{ // Scope B: Child Scope of Root Scope
    let b = 20 
    
    console.log(a)
    { // Scope C: Child Scope of Scope B
        let c = 30
    }
    

}
let a = 10
```

在這裡，對於Scope B而言，變數a的宣告定義會被系統認定為還未定義而無法被正常印出，這也代表宣告定義的先後順序會影響存取。 但若是在Child Scope之前就定義好要存取的變數，那麼可以在Child Scope來存取Parent Scope的元素。


### 例子3: 多個Parent Scope是相同的Child Scope是無法存取彼此間的變數


若我們考慮至少1個身在同個Scope的Child Scope，這些Child Scope彼此間的存取狀況會是如何？我們先繼續沿用上個例子來假設，首先我們在Scope B產生名為Scope D的Scope，現在我們有Scope C 和 Scope D，當我們想在Scope C存取Scope D的變數時，會因為宣告的先後順序而無法正常存取，而當我們想在Scope D存取Scope C下的變數時，我們會因為Scope C的變數已經被釋放掉而無法正常存取，換言之，Scope C 和 Scope D這兩者間無法彼此存取他們所擁有的變數。

```

// Scope A: Root Scope

let a = 10

{ // Scope B: Child Scope of Root Scope
    let b = 20 
    
    console.log(a)
    { // Scope C: Child Scope of Scope B
        let c = 30
    }

    { // Scope D: Child Scope of Scope B
        let d = 40
    }
    

}

```


## 補充資料


### var 宣告過的變數
var的不同處在於var是ES5標準以前就有的關鍵字，ES標準越前面其嚴謹性會比較鬆散，這使得他的作用域上往往會出現預期以外的事情，比如使區域變數有可能會升格為全域變數，而let、const則是ES6標準出現的，其作用域上會嚴格根據宣告所在來決定，當宣告在全域出現，那就是全域變數；若出現在區域上，那就是區域變數。

另外部分未使用var/const/let來宣告變數時，該變數很有可能因為預設關係變成var性質的變數，比如在一般模式下執行以下程式碼，其變數x會變成var性質的變數x，進而因其性質而使該變數升格為全域變數。

```
'use non-strict'
{
  for (x = 0; x < 10; x++) {
    
  }

}
console.log(x)                                  // 印出10

```


### 由括號所構成的區域作用域

除了一般由兩個括號所構成的作用域以外，函式、迴圈等用上括號的結構，其性質上會是在由括號構成的作用域

```
{
    statement1 / expression 1
               .
               .
               .

}
```

比如：

a. 函式：

```
function test() {

    let testval = 10 
    

    console.log(testval)        //result = 10
}
```

b. 迴圈：其中statement1至3也是在括號內部構成的作用域。

```
for (statement1; condition1; statement3) {
    // run something
}

```

可以將for迴圈看作是

```
{
    statement1;

    loop1:                      // loop1標籤

    if (condition) {            // 通常是條件式
        //run something
        statement3
        continue loop1;         // 會直接跳回loop1標籤所在的地方執行
    }
    
}

```

同樣地，while迴圈和do-while迴圈內的條件式可以看作是括號內部所構成的作用域：

```
while (condition1) {
    // run something
}
```

```
do {

} while (condition1)
```

可以分別看作是:

```
loop1:                          // loop1標籤
if (condition1) {
    // run something
    continue loop1;             // 會直接跳回loop1標籤所在的地方執行
}
```


```
loop1:                          // loop1標籤
// run something

if (condition1) {
    continue loop1;             // 會直接跳回loop1標籤所在的地方執行
}
```



參考資料：
1. https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/var
2. https://blog.bitsrc.io/understand-scope-in-javascript-e150f889ba72
3. https://8thlight.com/blog/jarkyn-soltobaeva/2017/06/13/scope-and-closures-in-javascript.html
