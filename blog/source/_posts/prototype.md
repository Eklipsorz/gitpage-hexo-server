---
title: JavaScript 的 prototype 簡介
date: 2021-11-27 20:24:48
tags:
 - JavaScript
 - prototype
---

## object oriented language
一個軟體的開發過程若都經由人類決定以及執行的話，那麼遲早會為了加速軟體的開發效率，而讓軟體的開發方式易於人類理解和使用，而物件導向語言正是其中一個案例，以易於人類理解的物件概念來建構出一項語言，在使用這項語言中會以物件(object)為一個開發上的基本單位，換言之，所有的程式碼皆會以物件本身來開發。

這個語言中，定義了物件本會有的性質：
  - 一個物件都如同世界上的每一個物件那樣，具有能夠進一步描述自己的屬性(property)以及進一步描述自己能做些什麼事情的方法(method)
  - 物件和物件存在著某種關係使其中一個物件擁有(繼承)另一個物件所擁有的屬性和方法，進而使物件們在屬性和方法很相似，比如兩個物件因為親子關係，擔任兒子的物件會繼承父母的物件所擁有的屬性和方法
  - 物件和物件不存在某種關係來使其中一個物件擁有(繼承)另一個物件所擁有的屬性和方法，而是在屬性和方法上可以統一成一個物件來分類，換言之就是以同種性質的物件但卻額外以不同形式、外型、結構來衍生出多個同性質且不同外形的物件，比如鳥和魚可以統一成一個物件

而對於以上性質的實現，程式語言上主要有三種實現概念分別對應它們：
  - Encapsulation：將程式碼以物件為軟體開發的基本單位來進行分類封裝，而每一個物件下所擁有的屬性和方法都皆封裝了對應的實作程式碼，進而讓開發方式都以物件來著手
  - Inheritance：允許物件A可以從而繼承(擁有)另一個物件B所擁有的屬性和方法，而物件A本身也可以擁有自己本身的屬性和方法
  - Polymorphism：允許多個物件整合成一個同種性質(某些屬性和方法相同的)的物件A，而物件A藉由相同性質以及附加額外屬性和方法來衍生多個物件們

不過實際上來說， 雖說Inheritance 和 Polymorphism 在概念上有些許的不同，但如果從實現他們的角度來看，這兩種都能從另一個類別A獲取相同的屬性名和方法名，只是唯一的差別就在於Polymorphism所對應的屬性值和方法值會是以不同於類別A的形式來存在，

> Polymorphism是描述同種性質的東西可以以不同形式、外形、結構來呈現/存在，比如同屬於動物的東西可以以有翅膀、有魚鰭、有爪子的形式來存在

因此可以將這兩個概念在獲取相同的屬性名和方法名或者定義繼承關係上實作成同一個機制，然後在對應屬性值和方法值這兩個賦予方式分別做出不同的機制來滿足他們原有的概念。


最後由於軟體開發上是以物件為基本單元來開發，但電腦是無法直接辨識物件以及不知道如何實作物件背後的屬性和方法，因此必須事先告知電腦物件是什麼的概念，而根據程式語言是否為直譯或者編譯來區分成兩種主要方法：
  - class-based：通常僅限於編譯語言，在編譯時期是先以類別來定義每一個物件是什麼樣子、具有哪些屬性和方法、與其他物件又具有什麼關係，執行時期則是按照具體定義/類別來執行。
  - prototype-based：通常僅限於直譯語言，由於不存在編譯時期，所以並沒辦法更早一步去定義物件，只能在執行期間產生出代表物件概念X的實體物件，而這個實體正是原型機(prototype，可藉由其本身來慢慢演進成對應物件的原型物件)，而該實體可藉由執行過程中添加屬性和方法來慢慢讓這個物件成形，並且允許每個物件都相關的屬性值來物件所屬的物件概念，從而實現定義物件的繼承，過程中，可能會使用物件的deep copy來實現Inheritance和Polymorphism，以避免多個物件共享於同一個參照或者同一個記憶體區塊。

參考資料：
[prototype based vs. class based inheritance](https://stackoverflow.com/questions/816071/prototype-based-vs-class-based-inheritance)



## JavaScript 物件導向
1. JavaScript 由於本身是直譯語言，只能以ProtoType-based 風格來實現物件導向這概念，但後來為了讓更多人適應JS而在ES2015之後提供class這關鍵字，但實際上只是被封裝後的語法糖(Syntactic Sugar)，其本質仍為ProtoType-based


## prototype 
prototype 的 原意是為了實現某些概念或想法而實做出來的第一個實體物，而往後的開發或者進展將會以該實體物為主，在這裡是以實現某些物件概念的第一個實體物件，本身會是定義這個物件概念擁有的屬性和方法，任意物件都擁有著prototype這屬性，而這屬性正是指定這物件是屬於哪一種物件概念，而屬性值會是代表該物件概念的實體物件，當任意物件透過prototype去指定任意一個物件概念，就能擁有(共享)該物件概念所擁有的屬性和方法，如物件1的prototype設定為物件2的prototype，那麼這兩個物件就能擁有(共享)同一個物件概念所擁有的屬性和方法，prototype屬性的設定方法：
	- 當透過new方法時，就會按照constructor綁定的prototype來設定
	- 直接透過prototype屬性值來設定


### prototype 定義
然而實際上prototype在概念上仍屬於實體物件，本質上仍需要額外內容來定義prototype具體所擁有的屬性和方法，當有了prototype具體的定義之後，就能依據其定義來建構出代表物件概念的prototype實體，而JS具體定義prototype所擁有的屬性和方法是透過函式和函式名稱來實現，函式名稱代表著prototype的名稱，而函式本身為該prototype的constructor - 負責建立對應prototype實體物件的函式，其中key為屬性或者方法，而value可以填入原型、物件、函式等，該函式若是以constructor形式來執行，會回傳一個prototype名為prototype1的物件
```
function prototype1 () {
  this.key1 = value1
  this.key2 = value2
        .
        .
        .
}
```

### prototype 例子
假設要定義一個名為person的prototype，在這個定義中會有名字和電子郵件，那麼內容會是如下，當person被當作以constructor形式來執行時，就回傳一個prototype名為person的物件
```
function person (name, email) {
  this.name = name
  this.email = email
}
```

### 透過prototype來建立實體
JavaScript允許開發者使用new關鍵字和代表prototype的函式來建立符合prototype的實體物件，在這裡的new會建立一個空實體物件，接著設定該物件的__proto__屬性為對應的prototype(new關鍵字之後的prototype名稱)，並將該空實物物件和value1, value2,... 等參數傳入至function prototype1 ()，而傳入進去的空實體物件在函式會是由this變數來儲存，在這裡會定義著該實體物件會有哪些新的屬性和方法，並按照指示將value1, value2,... 傳入至對應的新屬性和方法。

```
function prototype1 (value1, value2, ....) {
  this.key1 = value1
  this.key2 = value2
        .
        .
        .
}

const instance = new prototype1(value1, value2, ....)
```

另外JavaScript對於prototype的建構式的引數和參數之間的數量給予一定的彈性，不必兩者的數量一致就能允許執行，會按照對應位置來將引數對應至參數，而沒配到對的參數或者引數，會按照預設行為來處置，若有參數沒配到引數的話，其參數會被設定成undefined，而引數沒配到參數的話，就當即跳過。

題外話：此時若該實體物件是該prototype的第一個實體物件，那麼該實體就即可代表著prototype。
### 例子：透過prototype來建立實體
首先定義一個名為Employee的Prototype建構式，並試著用new和Prototype建構式來建立參數和引數數目不同的情況，一開始會沒給定引數(沒加括號)，接著給予空引數，緊接著給一個引數，這個動作會持續到增加第四個引數，其結果來看的話，沒給定引數會跟給予空引的結果都皆為沒給定對應的引數而讓參數為undefined，而給予一個引數的情況則是讓第一個參數有了配對而除他以外的參數皆為undefined，給予兩個引數，則都沒顯示undefined，若給予多於參數數量的引數，則會跟給予合適引數的情況一樣，每個參數都沒undefined，只是額外的引數會當即跳過。
```
function Employee(name, dept) {
	this.name = name
	this.dept = dept;
}

console.log(new Employee())
console.log(new Employee)
console.log(new Employee('sloth'))
console.log(new Employee('sloth', 'test'))
console.log(new Employee('sloth', 'test', 'test1'))
console.log(new Employee('sloth', 'test', 'test1', 'test2'))
```

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1637945033/blog/prototype/newInstanceExample_dv9x64.png)


### Object.prototype 屬性移除/增加
在JavaScript中可以在執行過程對特定原型進行屬性/方法上的增加和移除，通常增加方式為以下形式，其中constructor為作為原型定義的建構函式，prototype則是指constructor所擁有的原型物件，key則是該原型物件的屬性，而value就是對應值，由此來對特定屬性來填入任意值或者任意物件

```
constructor.prototype.key = value
```

而移除方式為以下形式，其中delete為刪除特定屬性的關鍵字，而constructor.prototype則是指建構函式所擁有的原型物件，key則是該物件上的屬性，由此來刪掉特定屬性(含屬性值)
```
delete constructor.prototype.key
```

## prototype 實作繼承方式

### class-based chain
會透過事先編譯來使用類別(class)來定義物件是什麼以及物件之間的關係是什麼，其中物件之間的關係無非就是繼承關係，來讓物件們擁有著相同的屬性和方法，它們具體實現的方法就是提供一些關鍵字來讓開發者方便定義哪些類別是屬於繼承關係，接著在編譯時期讓系統根據這關鍵字去決定每一個類別A是繼承繼承哪些一類別B，而這些類別B又是繼承哪些額外類別C，後面類別以此類推，在這裡系統為了更好去定義這些類別之間的繼承關係，而使用近似於Linked List結構來定義這些類別，而這個結構被稱之為類別鏈(class chain)。

在上述的描述中，我們可以得知類別A繼承類別B，而類別B又繼承類別C，那麼類別鏈就能以C->B->A來表達這樣子的關係，而每當要建立實體時，便會依據這條類別鏈來決定這實體所擁有的屬性和方法是為何，或者進一步決定哪些屬性和方法是由哪些類別來決定，同時也透透過類別鏈來進一步管理每一個類別。整體而言，類別鏈在編譯時期就決定並且定義了每一個類別之間的關係是什麼。

### prototype chain
而在JS的prototype-based的物件導向風格，一切都只能從執行中來決定每一種物件概念的prototype(相當於class-based的class)以及定義每一個prototype之間的關係，定義每一個prototype之間的關係會模仿著class-based的類別鏈概念而構建出一種可以在執行中來決定關係，也就是原型鏈(prototype chain)，透過賦予每個實體物件一些屬性以及方法來將實體物件綁定於代表物件概念A的prototype A來描述這些實體物件是屬於物件概念A的產物，而prototype A本身又是實體物件，所以可以進一步找到prototype A 是屬於哪個物件概念，後面prototype可以依此類推，那麼每當建立實體或者存取實體便會依據著這原型鏈來從中定義這實體物件究竟繼承了什麼以及哪些屬性和方法是被繼承的。

題外話：若持續對著該Object探尋著它的prototype則會找到null。


### prototype 實現方式
從前面來描述來看，原型鏈(prototype chain)是定義藉由層狀結構來定義每一個物件所屬的prototype跟其他prototype存在著什麼樣的繼承關係，在JS世界中，原型鏈(prototype chain)最頂端的prototype會是JavaScript Object本身，而所有的物件都皆從Object來進行著繼承或者串連成原型鏈。JS具體構成繼承或者原型鏈的方式會是使用著代表物件概念的prototype名稱和每個函式所擁有的prototype屬性來設定原型鏈，但這只是設定原型鏈，物件屬性還未真的繼承，必須再讓繼承的物件透過call和base方法去讓自己屬性去呼叫被繼承方的constructor來設定繼承的物件的屬性和方法為何，才能算真正的繼承。

所以若要讓屬於prototype A的實體物件A去繼承實體物件B所屬的prototype B之屬性和方法，整體來說有兩個首要任務：
1. 將prototype B 設定在prototype A本身對應的constructor的prototype屬性，從而構成Object->B->A這原型鏈
2. 在prototype A本身對應的constructor中設定方法來呼叫prototype B對應的constructor，並把想傳入的參數以及prototype A實體物件傳入進去，讓prototype B的constructor去將參數值設定只有它擁有的屬性和方法並增加至prototype A實體物件，使得prototype A實體物件擁有prototype B的屬性和方法。

舉一個例子： 在這裡假設有二個用來定義prototype內容的函式(建構函式)，這兩個prototype的名稱分別為prototype1和prototype2，而建構函式的參數分別為something1和something2，這些是用來設定建立對應實體時所要有的實際屬性和實際方法，
```
function prototype1(something1) {
  // define something1
}

function prototype2(something2) {

}
```
當要定義名為prototype2的prototype去繼承prototype1的屬性和方法時，會透過函式特有的prototype屬性來實現繼承，這時就定義了Object->prototype1->prototype2這原型鏈。

```
function prototype1(something1) {
  // define something1
}

function prototype2(something2) {

}

prototype2.prototype = prototype
```
緊接著就是為prototype2的實體物件去(繼承)增加只有prototype1會有的屬性和方法並依據prototype2的constructor給定的參數來賦予，在這裡會在prototype2使用call來實現，當要建立prototype2的實體物件時，prototype2會呼叫prototype1的建構式，並且將prototype2對應的實體物件和參數傳入至prototype1的建構函式，讓它去為prototype2對應的實體物件增加屬性以及根據參數來設定屬性值，當執行完call的實體物件就會擁有著prototype1會有的屬性和方法。

```
function prototype1(something1) {
  // define something1
}

function prototype2(something2) {
  prototype1.call(this, something3)
}

prototype2.prototype = prototype
```

### prototype 子類
在ES2015之後，JS就提供一系列class-based會用到的關鍵字和語法來封裝prototype based的概念，這些class-based關鍵字和語法在JS上就相當於語法糖的存在，在這裡若用上這些語法糖時，會用類別(class)稱呼prototype，而子類別(class)就是指繼承於其他prototype的prototype，拿上述的語法來當例子，要用class-based的語法定義兩個prototype/類別會是如下，

```
class prototype1 {
  constructor(something1) {
    // defefine something1
  }
}

class prototype2 {
  constructor(something1) {

  }
}

/* 
以上相等於下者
function prototype1(something1) {
  // define something1
}

function prototype2(something2) {
 
}

*/ 
```
其中每一個class下會有constructor這函式，這是對應類別的建構函式，當使用new 關鍵字來配合著 prototype 名稱就會呼叫著對應constructor來建立物件，而建立過程會如同建立prototype對應的實體物件一樣。

```
let object1 = new prototype1()
// call constructor of class prototype1
```

接著若要透過class-based語法來進一步實現繼承功能，會使用到extends和super這兩個關鍵字，其中extends是用來指示目前類別/prototype是繼承哪一個類別/prototype，而super則是代表著被繼承的prototype物件本身，在這裡一樣會定義兩個prototype，分別名為prototype1和prototype2，不同的事情就是透過extends來讓prototype2去繼承prototype1所擁有的屬性和方法，在這裡，prototype1會是prototype2的父類別，而prototype2就是prototype1的子類別，最後再讓prototype2的實體物件透過super來呼叫prototype1的constructor來讓該實體物件正式獲取prototype1所擁有的屬性和方法。

```
class prototype1 {
  constructor(something1) {
    // defefine something1
  }
}

class prototype2 {
  constructor(something1) {
    super(something3)
  }
}


/* 
以上相等於下者
function prototype1(something1) {
  // define something1
}

function prototype2(something2) {
  prototype1.call(this, something3)
}

prototype2.prototype = prototype
*/ 
```

### prototype屬性：prototype vs. class
prototype 和 ES2015所提供的語法糖在prototype相關屬性的設定大致上都會是一致，但除了屬性和方法在實際原型上的儲存會是不一樣以外，當使用prototype原生語法去定義每一個prototype會有的屬性和方法時，其constructor.prototype、object.\_\_proto\_\_會是prototype名稱和其擁有的屬性和方法，而當使用class語法糖去定義時，其constructor.prototype、object.\_\_proto\_\_只會是prototype名稱，不會儲存它所擁有的屬性和方法。

## prototype 相關術語
1. prototype：本身是用以實現某些物件概念的第一個實體物件，本身會是定義這個物件概念擁有的屬性和方法，實際上，該物件是第一個透過能夠建立某種物件概念的Constructor之實體物件，但本質上並不能完全是一般實體物件，此外，它代表著每一個物件所屬於的物件概念是為何。
2. Constructor.prototype: 每一個構造函式(Constructor)能夠具有的屬性之一，會指向該構造函式自己本身所具有的原型物件(prototype)，然而在語法上是可以允許一般函式是擁有prototype屬性
- 若是以一般函式(未以new來構成構造函數)的話，由於會指向構建function的函式所具有的原型物件，而那個原型物件正好是JavaScript Object本身。
```
function example(parameter1) {

	this.parameter = parameter1
}
console.log(example.prototype instanceof Object)
```
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1637937478/blog/prototype/normalFunctionExample_xl5yre.png)


- 若是以構造函數Manager的話，會是對應該構造函數Manager本身的原型物件，在這裡由於是設定Employee的第一個實體物件來代表，所以它在判別是否為Employee的實體物件的情況下會是true，且這也代表著Manager是基於Employee這物件概念往外延伸的另一個物件概念。

```
function Employee() {
	this.name = "";
	this.dept = "general";
}

function Manager() {
	Employee.call()
	this.reports = [];
}

Manager.prototype = new Employee;

console.log(Manager.prototype instanceof Employee)
```
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1637937478/blog/prototype/constructorExample_ha30k1.png)

3. Instance.\_\_proto\_\_: 是任何物件的屬性之一，指向物件所屬的構造函數所擁有的原型物件(prototype)
 - 若將Instance設定為構造函式 Manager時，由於構造函式本身是物件，所以系統會根據構造 "構造函式Manager" 的構造函式來找，但該構造函式Manager本身就是函式，系統會最終找上構造 "函式" 的構造函式，而它所擁有的原型正是JavaScript Object。
```
function Employee() {
	this.name = "";
	this.dept = "general";
}

function Manager() {
	Employee.call()
	this.reports = [];
}

Manager.prototype = new Employee;

console.log(Manager.__proto__ instanceof Employee)
console.log(Manager.__proto__ instanceof Object)
```
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1637938539/blog/prototype/__proto___example_mw95l5.png)

4. Object.\[\[prototype\]\]：是ECMAScript標準下的產物，等同於Object.\_\_proto\_\_，並指派Object.getPrototypeOf() 和 Object.setPrototypeOf()來存取每一個物件下所擁有的構造函數所擁有的原型，但由於大部分瀏覽器在推廣之前就已經先用\_\_proto\_\_來代替它實現：
  - Object.getPrototypeOf(obj) ：根據obj物件所對應的構造函式來獲取函式所擁有的原型
  - Object.setPrototypeOf(obj, prototype)：對obj物件所對應的構造函式所擁有的原型設定成指定原型prototype

參考資料：
1. [js中\_\_proto\_\_和prototype的区别和关系？](https://www.zhihu.com/question/34183746)
2. [What is the difference between prototype and \_\_proto\_\_ in JavaScript?
](https://javascript.plainenglish.io/proto-vs-prototype-in-js-140b9b9c8cd5)
3. [深入了解物件模型](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Details_of_the_Object_Model)
## 補充知識
### polymorphism
1. the fact that something such as an animal or organism can exist in different forms
2. 描述同種性質的東西可以以不同形式、外形、結構來呈現/存在，比如同屬於動物的東西可以以有翅膀、有魚鰭、有爪子的形式來存在


### object copy
1. 將一個物件A所擁有的內容複製到另一個物件B，在這裡由於每一個物件都具有屬性和方法，若物件A內容被複製到成為物件B的屬性和方法，那麼這兩個物件所擁有屬性名稱、對應屬性值、方法名稱、對應方法皆會一樣。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1638015712/blog/prototype/begintoCopy_wtckkf.png)
2. 但若屬性值或者對應方法本身是一個參照或者一個記憶體位址的話，那麼物件A和物件B很有可能會一起共享著同一個參照或者同一個記憶體位址，只要任何一方針對該參照對應的內容做更動，另一方就即可透過同一個參照看到更動內容，而若拷貝結果是這樣，那麼該拷貝方式就即為淺度拷貝(Shadow Copy)
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1638015712/blog/prototype/ShadowCopyResult_gchjms.png)
3. 若物件A內容被複製成為物件B的屬性和方法時，也連同替物件A的所有參照對應的物件內容進行複製，而非單純複製參照位址：將整個內容複製給物件B，進而讓雙方不再共享同一個參照或者同一個記憶體位址，那麼任何一方做任何更動時不會影響另一方的內容，而若拷貝結果是這樣，那麼該拷貝方式就即為深度拷貝(Deep Copy)

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1638015711/blog/prototype/DeepCopyResult_wgeumz.png  )

參考資料：
1. [object copy](https://en.wikipedia.org/wiki/Object_copying#Shallow_copy)