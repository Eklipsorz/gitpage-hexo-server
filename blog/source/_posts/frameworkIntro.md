---
title: 框架概念介紹
tags:
  - framework
date: 2021-10-15 21:31:13
---


## 框架

框架則是一種以某種開發環境A的不足地方再加以改良的開發環境B，從而提供不足的功能給開發者，而開發環境B會以自己專屬的概念、語法、功能來提供，這些概念、語法、功能將會對應開發環境A的原有概念、語法、功能以及額外包覆著其他模組，開發者只需要在被開發環境B封裝的環境下進行想要的開發，最後會由於開發者的程式碼被開發環境B封裝的關係而先讓開發環境B先被執行，然後再由它來決定開發者的程式碼何時能被執行，換言之，你實際的應用程式是被一個另一個程式(開發環境B)包含住，然後這個程式可以為你的應用程式提供許多支援性，只是每當執行時，就會先以這個程式來執行，等這個程式需要你的應用程式時才會由程式去呼叫。

另外，由於該環境B為了改良而以著專屬的語法、概念、功能來對應原有環境下的概念、語法、功能，所以會以這樣的特性自成一個體系、框架、結構等，所以才以框架來稱呼。

## 後端框架 vs 函式庫

函式庫是一種存放可重用性高的程式碼、函式、類別的儲存庫，可以藉由函式庫呼叫來實現相同類似功能的程式碼來執行，不必額外花時間去重新打造相同的程式來增加開發效率，而這些程式碼通常會由許多開發者構築而成的，你可以自己決定什麼時候使用儲存庫下的程式碼以及使用什麼樣程式碼。

對於不必重新打造相同類似的功能而言，框架可以在某一種程度上能做得到，但是其本身是另一套擁有專屬語法、概念、功能的開發環境，所以本質上無法讓開發者自由自在去調用框架下所提供程式碼，開發者必須遵守框架所定下的語法、概念才能被執行，而執行方面會是由框架來呼叫開發者的程式碼，而非像函式庫那樣，由開發者去呼叫函式庫的程式碼。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1634139009/blog/SE/frameworkVSLibrary_twgjx3.png)

## 用語介紹
1. 框架(Framework)和函式庫(Library)可以用工具包來形容他們，而工具包本身意思就是將多個模組/多種不同程式碼封裝成一個空間，形容工具包的英文可以是Package、Library、Module 來形容，其中Package在Javascript社群中特別常形容這兩者，另外部分用語可能無法通用於兩者，比如Library和Framework本質上並不能劃上等號。
2. Package的原意為被紙包覆住的物件，在這裏可以指被用另一個開發環境包覆住開發者的程式碼的框架、程式碼被封裝在一起的函式庫(Library)
3. 例子：Bootstrap、Font Awesome、Axios
 - 我們運用 Bootstrap 來做出風格一致的網站樣式
 - 我們用 Font Awesome 來做 icon
 - 我們用 Axios 來發送 Ajax 請求

### 參考資料
1. [The Difference Between a Framework and a Library](https://www.freecodecamp.org/news/the-difference-between-a-framework-and-a-library-bd133054023f/)
2. [Library vs. Framework?](https://www.programcreek.com/2011/09/what-is-the-difference-between-a-java-library-and-a-framework/)
