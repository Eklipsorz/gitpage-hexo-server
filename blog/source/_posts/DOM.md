---
title: DOM - 節點的構造 
tags:
  - HTML
	- DOM
categories:
  - Website Development
date: 2021-08-27 19:33:10
---



DOM (Document(註1) Object Model)是將HTML檔案本身內容轉化多個物件或者多個節點，並將這些物件/節點組合成樹狀結構。每一個節點(Tag 1)都帶有一些子節點來表示對應元素的HTML屬性值(id和class)、文字內容、原本對應元素在HTML所包含的元素/節點(tag，帶有其他子節點的節點或者帶有子節點集合的標籤):

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1633109630/blog/dom/aDomNode_x6bv3g.png)


在這裡只有類別名稱和特定ID並不會真的在DOM被當作子節點來看待，其餘元素則會按照parent-child關係來區分父節點和子節，因此我們可以將其餘元素視為該元素下的子節點(Child node1~Child nodeN)，而被對應元素包含的對應元素(Tag 2)也會是該節點下的子節點。

另外我們也根據節點的用途來進一步區分每個節點的種類是為何：

- 若是HTML元素的話，會被當作是元素節點(Element Node)
- 若是代表元素的屬性(註2)，會被當作是屬性節點(Attribute Node)
- 若是代表一般的文字內容(本身並無特別意義)，會被當作是文字節點(Text Node)
- 若是代表HTML檔案上的註解內容，會被當作是註解節點(Comment Node) 


以一個例子來說明一個元素(標籤)在DOM中會是什麼樣子，在這裡我們以p標籤為例子，並給予p標籤一些屬性值，比如class和id，另外再讓p標籤去包含一般文字、em元素、註解。
```
<html>
   <head>
	<title>DOM</title>
   </head>
   <body>
	<h1>Hello, World!</h1>
  	<p class="class1 class2" id="id1">
		<!-- TEST COMMENT -->
    		This is a <em>simple</em> website.
 	</p>
        
</body>
</html>
```

經過瀏覽器解析而轉換成DOM後，其p標籤會如同下圖那樣，標籤以及被包含的標籤會被當作元素節點(以橘紅色來標示)，原本標籤上的屬性值會是屬性節點(以藍綠色來標示)，而子節點出現順序將會以HTML檔案的讀取順序來決定，越先讀取的就放的越前面，首先我們會看到由"TEST COMMENT"所構成的註解節點(以綠色來標示)，接著就是以"This is a "所構成的文字節點，緊接著是<em>元素節點，最後是" website."構成的文字節點(以淺藍色來標示)，而<em>元素節點還會包含著"simple"所構成的文字節點(以橘紅色來標示):

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1630055616/blog/dom/aDomNodeExample_ncvwwt.png)

## 註解
1. Document Object Model中的Document是指HTML檔案本身
2. 在HTML語法中，除了可以用<tag></tag>來定義其tag對應的元素以外，還可以在括號內部增加屬性值，來進一步描述其元素在HTML會呈現的樣子，而該屬性值包含了class、id、href、src等等，這些屬性值將會在DOM架構中被當作屬性節點。

## 參考資料
1. children 和 childNodes 的差別，https://www.geeksforgeeks.org/what-is-the-difference-between-children-and-childnodes-in-javascript/
2. HTML 屬性，https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes
