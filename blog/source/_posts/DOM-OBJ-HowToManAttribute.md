---
title: DOM - How To Manipulate Attribute From Object
tags:
  - HTML
categories:
  - Website Development
date: 2021-08-28 22:00:44
---


在JavaScript中，我們可以透過classList、style、className來更動對應網頁元素的樣式是為何，甚至可以依據某些事件發生而變動。在本文中，我們將簡介這些方法


## Node-Attribute: classList

NODE.classList 指元素節點所擁有的屬性之一，其值會對應著具有live特性的DOMTokenList物件(註1)，該物件會儲存其對應元素使用的所有樣式名稱，每一個使用的樣式會以單獨一個元素儲存在DOMTokenList物件。

### classList Object: Method 

NODE.classList.add(className1,...., classNameN)：對元素節點所擁有的DOMTokenList進行其他樣式名稱(className1,...classNameN)的增加，而被增加進來的樣式名稱會從DOMTokenList的尾部位開始放入，而參數量(可被放進去的樣式數量)則不限定於1~2個，可以按照開發者的需求而不斷放入。

另外要指定的類別名稱必須事先指定好該類別下所擁有的屬性以及其屬性值是為何，否則會
因為無法找到對應的類別名稱而設定預設樣式。

NODE.classList.remove(className1,..., classNameN)：從DOMTokenList物件刪除指定樣式名稱，而className1至classNameN則是依據開發者而定，沒限定於1~2個。


## Node-Attribute: className
NODE.className 屬性是指元素節點所採用的屬性(尤指類別)節點是什麼或者採用的選擇器是什麼，該屬性可允許開發者讀取和寫入，若元素節點NODE搭配多個類別，那麼其值會是下面形式來表示，每一個類別之間都會有空格作為間隔，這代表著對應元素使用著class1、class2、....、classN這幾個類別。

```
"class1 class2 ..... classN"
```

若要替元素節點NODE進行類別或者樣式的變更，則可透過相同規則來進行，另外，每一次的變更會連帶改變具有live特性的DOMTokenList物件。
```
NODE.className = "class1 class2 .... classN"
```

另外要指定的類別名稱必須事先指定好該類別下所擁有的屬性以及其屬性值是為何，否則會因為無法找到對應的類別名稱而設定預設樣式。

## Node-Attribute: style
style是元素節點的屬性之一，用來表示目前對應網頁標籤元素在style所定義的屬性值，style會使用CSSStyleDeclaration 物件(註2)來儲存對應的HTML標籤上所擁有的style屬性，舉一個例子，在span標籤下設定style屬性來證明元素節點下的style值會跟標籤的style值是一致：

```
<span style="color: red;background: blanchedalmond;">My Website</span>
```

那麼在元素節點下的style內容會是：從右半邊console去抓取span標籤元素，並且利用cssText(註3)來方便觀察元素節點下的style內容會是什麼，而左半邊的紅色字體正是用span標籤元素來呈現。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1630158193/blog/dom_Manipulation/styleExample_ptjfbo.png)

從結果可以發現元素節點的style值會跟標籤下的style屬性是一致的。另外若把標籤下的style去掉的話，

```
<span>My Website</span>
```

其結果會是：右半邊顯示的style是因為對應標籤的style內容不存在而變成空字串，而左半邊的span標籤元素則回到預設的樣式。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1630158193/blog/dom_Manipulation/noStyleExample_unvazz.png)


從這兩個例子可以明顯看出標籤和元素節點下的style值都是一樣的且保持著同步。另外由DOMTokenList物件擁有著對應元素所能夠設定的樣式屬性名稱，比如字體大小、字體顏色、背景顏色等等，這些屬性名稱皆是該物件的屬性，可以藉由這個特性來設定特定的樣式屬性值。
```
NODE.style.styleName: styleValue
```

當然，當你透過上面方法來更動樣式屬性名稱時，便是代表連動更新元素節點下的style值以及標籤元素下的style值。



## 註解
1. DOMTokenList物件是類似於陣列的物件但又不是陣列，儲存該物件的元素皆會以index做為綁定，可以透過index來存取對應元素。
2. CSSStyleDeclaration 物件是key-value pair的集合，其中key是元素會用到的樣式屬性名稱，而value則是對應的屬性值。
3. cssText 是 CSSStyleDeclaration 物件的屬性，只會回傳原本在對應的HTML標籤所擁有的style屬性值。


## 參考資料
1. DOMTokenList，https://developer.mozilla.org/en-US/docs/Web/API/DOMTokenList
2. Element.classList，https://developer.mozilla.org/en-US/docs/Web/API/Element/classList
3. CSSStyleDeclaration，https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleDeclaration
4. NODE.style，https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style
