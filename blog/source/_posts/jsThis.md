---
title: JavaScript - This 變數
date: 2021-07-30 00:38:50
tags: JavaScript
---


為了方便在object的method下使用該object的屬性或者其他method，而建立一個名為this變數，而這個變數會是指著object本身。 另外每一個function（非簡化版本）都擁有this這內建變數，只有arrow function不存在this

舉例來說：

假如我們建立一個object給user變數(此變數就成了該instance的reference或者別名)，該object的屬性為name，而方法(method)則為speak()函式

```
const user = {
      name: 'apple',
      speak() {
        console.log('my name is')
      },
 }
```

，每當我們想要呼叫該object的speak方法時，會產生
```
my name is
```
可如果說想讓這方法去印出object本身的name，那麼就能利用下面語法來印出，其中this就這個user指向的object本身，而name則是object所擁有的屬性name

```
speak() {
        console.log('my name is', this.name)
      },
```
，結果會是：

```
my name is apple
```


## this 本身內容

this本身會根據呼叫者是否物件、被呼叫的函式(方法)是否為arrow function來決定指向哪個物件。

### 當呼叫者是物件本身且方法為一般函式(非簡化)
在這種情況下的方法會是以下形式，若我們建立了一個物件並給予person1這個別名（reference)
```
function functionName {
    Statement
}
```

，其object會是以下內容，

```
const person1 = {
    name: 'John',
    myNameIs() {
        console.log('my name is', this.person1)
    }
}
```
當我們利用person1下的myNameIs方法時，系統會解析呼叫myNameIs的物件是誰，在這裡系統會發現是person1所指向的物件呼叫該方法的

```
person1.myNameIs()
```
，接著它就直接該物件下的myNameIs()方法，同時它也會發現this這變數，在這裏由於系統已經發現是person1所指向的物件，所以this會跟著指向同個物件並允許系統獲取該物件的name，也就是John，最後螢幕顯示的結果會是：

```
my name is John
```

可如果這時我再添加另一個person2來指向另一個新的物件時，比如說

```
const person2 = {
    name: 'Sam',
    myNameIs() {
        console.log('my name is', this.person1)
    }
}
```

那麼同樣地，我們再次下達以下程式碼：
```
person1.myNameIs()
person2.myNameIs()
```
系統會根據先前的解析方式來分別從person1和person2所指向的物件來獲取name的資訊並顯示以下結果

```
my name is John
my name is Sam
```

### 沒有物件的時候，呼叫時的this會是？
可如果呼叫的函式存在this，但呼叫時卻沒有物件方便讓系統辨識this會指向誰。

```
function myNameIs() {
    console.log('my name is', this.name)
}

myNameIs()
```
這時系統會預設讓這個this變數指向window這個global object，但由於window沒有name這個屬性，所以得到的結果會是：

```
my name is undefined
```


### 當使用arrow function的this會是？

由於arrow function本身沒有this變數，但實作上會以該function定義所在的環境是否存在其他this內建變數，若存在的話就以該this變數所指向的object為主，若不存在的話就以window這個global object為主。


舉例來說：

```
const arrow = () => {
    console.log(this)
}

arrow() 1.

const user = {
        name : 'John',
        arrow: () => {
            console.log(this)
        },
        speak() {
            arrow()
        }
}

user.arrow() 2.
user.speak() 3.
```

程式碼中存在1.~3.這三處，第一處呼叫了位於global scope的arrow function，由該函式本身就沒有this變數，所以this會被定義成window，而第二處則是呼叫user變數指向的object之arrow方法，該方法由於定義的範圍並不存在其他this變數，因此他的this也是window，而第三處的this則是間接呼叫位於global scope的arrow 函式，所以也跟第一處一樣是指向window這object。


另一個範例為：

```
var func = function () {
  var func2 = function () {
    setTimeout(() => {
      console.log(this); 
    }, 10);
  };
  
  var func3 = {
    func: func2,
    var4: 4
  }
  func2(); // this = window
  func3.func(); // func3 Object
}
func(); 
````

藉由func來執行整個程式碼，其中我們只需要關注func2()和func3.func()這兩個函式呼叫，func2呼叫由於是本身調用週期工作來印this，this被定義到的範圍是位於func2，而他的this變數指向window，所以週期工作的this也跟著指向window，而func3.func()則是因爲func3本身是物件且func會指向func2這函式物件，這相當於func3.func2()。


由於這樣，而func2的this變數就跟著指向呼叫者-func3，因此週期任務也會跟著指向func3


## 參考資料：
1. https://shubo.io/what-is-this/#什麼是-
2. https://www.w3schools.com/js/js_this.asp
3. https://wcc723.github.io/javascript/2017/12/21/javascript-es6-arrow-function/


