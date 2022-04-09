---
title: 筆記：Web API 前後端開發協作形式
date: 2022-04-09 19:31:22
tags:
 - Web API
categories:
 - API
---

## 背景
在開發網頁前後分離專案時，前後兩端總是需要一份API文件來確定資料該如何調用、參數要什麼、回傳什麼、具有什麼功能，但對於整個開發流程而言，傳統上是採用**Code first概念**，API文件會在後端完成整個API邏輯層面的程式碼完工後才會公開，但這樣所花費的時間會使前端無法直接使用API來完成後續前後端的對接，進而讓整個專案因API文件拖到後頭才生成而被延宕，而且完工後的API文件也不見得能讓前端方便對接，總是需要時間去協調該文件對應的程式碼以及文件該如何修改才能解決。

為此就衍生出**API-first 概念**，來讓前後端人員以API文件來協調、確定，接著確定之後，在按照API文件打造一個假的後端伺服器以供前端使用，在這樣子的過程中，前端直接連接該假的伺服器來完成對接，從而讓前端不會再被後端的實作給延宕，而後端可以各自以自己的權責來同時完成手邊的任務，過程中很有可能因為前後端對於功能、規格上具有一定程度的衝突而修改API文件、假的伺服器，但這些因素換作是傳統概念也會遇到，所以是可以容忍的。最後當後端完成真正的伺服器時，前端只需要改連接至真正的伺服器就能直接用。

## 發生過程
當商業團隊發現可行的產品想法時，會在進一步將想法轉換成具體如何實現的產品構思和企劃書，接著傳遞至實際開發部門來探討如何實現，這時開發部分可以有兩種選擇：
  - API first：基於產品構思、企劃書來定義API文件以及Mock，隨後再來根據文件先實作Mock Server，然後讓前端先調用Mock Server進行資料和畫面的對接，後端再來從中按照API文件來開發真正的Server，最後確定完成真正的Server 就替換掉Mock Server 
  - Code first：基於產品構思、企劃書來先讓後端實作API實際對應的業務邏輯，隨後完成後在編寫API文件以及提供Server 給前端對接
![](https://static1.smartbear.co/swagger/media/blog/design-first-vs-code-first-swaggerhub-graphic.png)
## API First
API first 又名為Contract First，主張前後端協作**先以基本需求的文件來建立API(包含其API規格/合約)為主，而隨後按照API來實作**，在這裡API文件會被視為一份有關於前後端能夠達成一致的API合約，合約形式通常會是：
  - OPEN API
  - API Blueprint：Markdown Syntax for Object Notation形式，以Markdown形式
合約上的語言將會是以人類和機器皆能看懂和能夠解析的語言，為的就是一方面讓人類很好理解並開發對應合約，二方面就是能夠讓電腦解析合約語言並產生對應更為看得懂的合約形式。

參考資料：
[Design First or Code First: What’s the Best Approach to API Development? ](https://swagger.io/blog/api-design/design-first-or-code-first-api-development/)

Note:
1. Contract: 一份代表雙方在某件事上正式達成一致的正式文件
> A legal document states and explains a formal agreement bewteen two different people or groups


### API First + Mock 
除了確定API合約的內容以外，還得確定目前的API合約是否符合前端開發者(使用者)為所需，所以會依據目前約內容來打造Mock Server或者稱之為假的API Server，在這裡的Server會是以回傳內容和對應行為來模仿著後端最後所要實現出來的API Server，具體來說，依照目前合約內容而設定在它之下的每個API端點都具有設定對應的回傳內容以及簡單的回應動作(如移除資料、增加資料)。

客戶端可把Mock Server當作是真正的API Server來對接，等到後端開發完真正的API Server後，就直接替換掉Mock Server，過程中可以提前解決前後端一定會遇到的對接衝突問題(P.S. 即使替換成Code First進行，也一定會遇到的衝突問題，並且 **以更動Mock Server的回應和簡單回應動作的形式** 來快速解決對接問題並給予前端快速對接，而後端也能按照問題優先權來安排實際的修正，最後也能藉由代表目前合約內容的Mock Server來驗證目前API合約內容是否合乎實際需求，使前後端的開發都能夠保持正確並一致的狀態下開發。

參考資料：
[[面試][後端]在正式 API 完成前，如何讓要串接的工程師不要空等？](https://ithelp.ithome.com.tw/articles/10267680)
[淺談 API First](https://ruddyblog.wordpress.com/2021/09/08/%E6%B7%BA%E8%AB%87-api-first/)


Note：
1. Mock: 也稱之為Mock object， 意指為仿製的對象，也就是以行為或者外表來近似於某項人事物X但卻不是真實的人事物X的事務。
2. 引申至電腦科學中的物件導向程式設計，mock object是一個模擬某項物件X行為的模擬物件，該模擬方式會是以可控制的方式來模擬。
> In object-oriented programming, mock objects are simulated objects that mimic the behavior of real objects in controlled ways, most often as part of a software testing initiative. A programmer typically creates a mock object to test the behavior of some other object, in much the same way that a car designer uses a crash test dummy to simulate the dynamic behavior of a human in vehicle impacts. The technique is also applicable in generic programming.
3. Mock Server：原本是基於HTTP/HTTPS協定下的服務，主要功能為模仿特定伺服器下的特定服務並藉此給予使用者，其服務的模仿形式通常會是以具體回傳訊息以及特定具體行為來回應每個從客戶端發出的請求，如RPC-style 服務或者RESTful 服務。


### API First 適用時機點
  - 考量到擁有較好的Developer Experience
  - 考量到需要交付較重要的API給客戶，尤其是需要一份契約來給予雙方信任的情況
  - 考量到團隊對於API的一致了解、溝通問題：人們可以透過易懂的文件中找出程式碼潛在的問題並給予修改建議，同時也讓使用者更快速暸解如何使用。



## Code first
Code first 主張 **先以基本需求的文件來實現API的對應業務邏輯，之後再從實現程式碼中定義API的形式**，通常這種會是以API的迭代速度來犧牲掉開發前期所需的前後端對於API的共同認知確定，而這樣通常會因為認知問題而增加溝通成本，且有可能會讓前端被迫等待後端完成才能進行對接，進而影響開發效率。

### Code First 適用時機點
  - 若以快速投入市場和敏捷性作為成功的API形式
  > This is important if your go-to market strategy emphasizes speed and agility as important factors for the success of the API program. The fact that automation is much easier in the code-first approach helps strengthen this case, with a lot of libraries supporting scaffolding server code, functional testing, and deployment automation. 
  - 考量以團隊內部、架構較小的API，使用Code-first會較為理想，畢竟受到影響的層面已經縮小至內部，而非到外部。

