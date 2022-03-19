---
title: HTML屬性 VS. 物件屬性
tags:
  - DOM
date: 2021-10-03 22:12:40
---



## 導覽簡介
首先先來考慮原有文字的含義，接著分別介紹著HTML屬性和物件屬性，最後再說明HTML經過轉變化的屬性又是如何變化。

## 這兩者間文字含義
HTML的屬性是用attribute來表達，而物件的屬性則是用property來表達，雖然這兩者在中文翻譯上是表達屬性，但真正含義都是在原文是如何表達他們，前者的attribute是指附加至元件/物件/物體的額外資訊，這些資訊通常會強調著元件/物件/物體 "具有" 什麼樣的功能或者什麼樣性質，不會被特意強調元件/物件/物體本來就會有的，而後者的property是指著元件/物件/物體上的原有特性，比較強調著物件 "本來就會有" 的性質。

### 在程式語言上的含義

在程式語言上，HTML本身利用標籤和內容來呈現網頁的呈現，標籤正是網頁的元件，為了進一步呈現更豐富更自由的網頁內容，而添加資訊(比如href、src、id、classu)至標籤上，使瀏覽器知道透過這些資訊來改變元件的特性，而這些額外資訊正是attribute，在這裡我們可以將attribute稱之為元件屬性，然而沒用property來描述是因為這些資訊本來就不是這些元件與生俱來的，比較像是額外添加給的功能或者特性。

而物件導向語言是以一個物件的角度來開發程式，過程中會為了更充分表達物件 "原有" 的特性和行為，而選擇property來描述屬性，但若選擇attribute就沒特意強調著屬性是這個物件所與生俱來的特性，只是單純附加給這些物件一些資訊來描述特性。

## HTML屬性
根據描述，HTML的屬性(attribute)只不過是附加網頁元件的額外資訊，因此我們可以在對應元件的標籤添置一些資訊，形式會是如下的attributeName和attributeValue，

```
<tag attributeName=attributeValue>
  content
</tag>
```
前者是指定屬性名稱，後者則對應名稱的值，屬性名稱沒有大小寫之分並一律當作小寫來看待，因此你寫上大寫的ID，都會被當作id，而對應值在大部分情況下是字串值，名稱和對應值可以在滿足前面所述的規定下寫出與對應元件無關的名稱和值。但為了進一步利用屬性值來呈現更豐富的網頁效果，部分的屬性名稱被HTML標準化成每個元件皆擁有不同的屬性(attribute)，而這些屬性能夠若被瀏覽器辨識到會使對應的元件展現出額外的效果，而其餘屬性名稱則沒有任何效果。

## 物件屬性
物件導向語言中的物件所擁有的屬性(property)如同字面上的定義，會是描述物件原有的特性或者性質，通常會是以key-value pair的形式來表示屬性和屬性值，在JavaScript中，我們可以透過其動態語言特性在內容額外添加某個物件上的屬性或者事先定義一個物件會有屬性，形式上通常會是以下兩種，其中property則是屬性，而value是對應屬性的屬性值，第一種形式是透過內容額外添加屬性，第二種形式則是事先定義一個物件。

```
const obj = {}
obj.property = value
```

```
const obj = {
  property: value
}
```


## HTML轉變成DOM後

當瀏覽器載入頁面時，會讀取(解析)網頁並將網頁上的每一個HTML元件轉化成DOM節點或者DOM物件，而每個HTML元件上都有特定被標準化的屬性(attribute)來讓瀏覽器進一步辨識以及元件呈現，同樣地，這些被標準化的屬性在HTML元件轉化成DOM物件的過程中，瀏覽器會直接將標準化的屬性(attribute)和屬性值(attribute value)轉化成對應物件的屬性(property)以及屬性值(property value)，也就是說我們可以用物件的形式來表達那些attribute所帶有的額外資訊，比如說HTML元件為如下元件，轉化後的物件是body，其id屬性(attribute)本身因為為標準化的屬性(attribute)，所以會直接變成body物件的屬性(property)，而屬性(property)名稱會是原本的屬性(attribute)名稱，也就是body.id，而該屬性值會是"page"這內容。
```
<body id=“page”>
```

而不是標準化的屬性(attribute)則會因為瀏覽器無法辨識而不直接轉化成對應物件的屬性(property)，而這些案例通常是：

1. 屬性名稱未能夠正常辨識：something是這案例所要使用的屬性名稱，在這裡由於其名稱既不是body元件會有的屬性名稱，更不是正常會有的屬性名稱，所以無法被辨識

```
<body id="test" something="non-standard">
</body>
```

2. 同個能辨識出來的屬性名稱在其他不存在該屬性的元件使用：其中type是這案例所要使用的屬性，在input元件上因為它存在type這屬性名稱，所以能夠正常辨識，而body元件則是不存在type這屬性名稱，所以無法被辨識

```
<body id="body" type="...">
  <input id="input" type="text">
</body>
```


最後，瀏覽器會提供一個能夠存取DOM Tree的介面給想要存取它的程式語言，比如JavaScript，該語言只要一從這個介面來獲取DOM物件時，會直接轉化成JavaScript能夠辨識的物件型別，同時間也會把該物件所擁有屬性值(property)轉化成程式語言能夠存取的型態。



## 參考資料
1. [attribute vs. property](https://www.researchgate.net/post/What-are-the-differences-between-attribute-and-properties)
2. [Attributes and properties](https://javascript.info/dom-attributes-and-properties)