---
title: 探討：http 協議上的 session 概念
date: 2022-04-20 00:41:28
tags:
  - Session
  - Cookie
categories:
  - Website Development
---

## session 本身的含義

根據劍橋字典所描述的session，會是指特定活動下的一段時間或者特定活動下的一場會議，在這裡可以稍微轉換一下說法，一段時間可以用一場會議來替代描述session，那麼實際上專注在會議上的定義就能明瞭session的意涵，而meeting代表著一個計畫好的時間點，能夠與其他人討論著什麼東西的時間點，或者說計畫好的互動情形，添加至原句就 **使session的意思變成特定活動下的所預計好之互動情形、互動狀態** 
```
session: a period of time or meeting arranged for a particular activity
meeting: a planned occasion when people come together to discuss something
```

## session 在http協議上的含義
從上述轉換成http協議上的說法，**特定活動下的所預計好之互動情形** 的特定活動下就是意旨**伺服器和客戶端之間的連線活動下**，所預計好之互動情形則是意旨 **伺服器和客戶端之間在連線時的互動會根據RFC規範所定義的內容來進行**，換言之預計就是指著根據RFC規範下所會有的互動，簡單點就是指 **伺服器和客戶端之間在連線時的互動情形、互動狀態**

然而 session 僅僅只是描述著 **伺服器和客戶端之間在連線時的互動情形**，而非代表存在著能夠代表互動情形的資訊並儲存在相關的伺服器和客戶端

## session 搖身成為 stateful session
由於http協議本身為了簡化客戶端和伺服器之間的連線結構，而定義客戶端和伺服器之間在連線時互動情況並不會紀錄下來-無狀態(stateless)，隨著越來越多服務需要客戶端和伺服器之間的過去互動情況，比如首次登入後就直接透過互動情況而跳過、使用者可以透過過去的購物車內容來直接購買，為此，就有人謀生一個想法-能夠讓伺服器和客戶端紀錄過去的互動狀況，然而這些情景無疑可能會大改當時已經定義好的http設計，比如從無狀態更改正狀態(statefaul)協議。

因此為了在不違反http無狀態下的環境能夠滿足伺服器和客戶端對於狀態的需求，有人於RFC規範提出stateful session概念：
  - 主要使session抽象概念轉換成能用具體形式的資訊內容來代表session，並且讓下一次session中能夠直接運用代表過去session的資訊內容來進行，換言之，每個session都可以擁有過去session狀態來處理，而再也不會是每個session都帶著空白的狀態來處理，
  - 限制代表每一個session的資訊能夠在短時間內結束且有辦法主動終結，以確保stateful session概念不會完全違反http協議上的無狀態特性。
  >  There are, of course, many different potential contexts and thus many different potential types of session.  The designers' paradigm for sessions created by the exchange of cookies has these key attributes:
  >   1.  Each session has a beginning and an end.
  >   2.  Each session is relatively short-lived.
  >   3.  Either the user agent or the origin server may terminate a session.
  >   4.  The session is implicit in the exchange of state information.



## stateful session 具體實作
在stateful session概念下，勢必得讓客戶端和伺服器都能夠儲存連線時的互動情況下才能運用，也就是客戶端單方面紀錄與伺服器連線時的互動狀態 和 伺服器單方面紀錄與客戶端連線時的互動狀態。

具體實作方式為每一次只要客戶端X和伺服器Y之間建立連線，並且客戶端X向伺服器Y發送請求，那麼伺服器Y就會建立一個空間來準備儲存能夠代表著客戶端X和伺服器Y的session之資訊，然而這樣子的session只是伺服器單方面紀錄著與客戶端之間的互動狀態，還差了讓客戶端也單方面紀錄與伺服器之間的互動狀態


是讓客戶端和伺服器在每一次連線時可以各自從自己的系統中分配空間來儲存能夠代表與彼此進行連線時會有的互動情形之資料：
  - 從客戶端單方面紀錄與伺服器之間的互動情形
  - 從伺服器單方面紀錄與客戶端之間的互動情形
  - 更或者讓兩者紀錄雙方的互動情形

並於下一次的session延續其結果來進行處理，比如說客戶端曾與某個電商伺服器進行連線並從中將產品放進購物車，但過程中客戶端先暫時關閉伺服器連線，而為了讓客戶端還能回憶起購物車的產品，伺服器會要求客戶端以Cookie形式來儲存著這產品的相關資訊，其內容會是產品的主要內容


而具體實作方式則是允許客戶端和伺服器在每一次連線時可以各自從自己的系統中分配空間來儲存能夠代表與彼此進行連線時會有的互動情形之資料，而這樣的資料會是用session來稱呼，session 裡頭的內容則是稱之為session information，在下一次連線時，雙方可從session來延續過去的互動情形進行額外的處理，

實作中的客戶端會以Cookie資訊來稱呼session以及透過伺服器所給定的Set-Cookie封包來建立紀錄，接著讓客戶端每次向著曾經請求的同個伺服器發送請求時就會跟著附加Cookie資訊至請求封包，以讓伺服器延續先前的處理結果為客戶端處理；而伺服器紀錄客戶端連線時的互動情形，通常會直接以session來命名


總結：
1. session 本就是概念，與在實際實現上，伺服器的session相比，session就只是描述如何實現它和它是什麼，而伺服器上的session就是依據著前者概念來建立出實體的session。
2. session概念的存在意義是為了 **在不違反http無狀態下的環境能夠滿足伺服器和客戶端對於狀態的需求**
3. 通常，session概念所對應的實現分別在伺服器和客戶端具有不同的名字：
  - Cookie：屬於客戶端，是伺服器麻煩客戶端去紀錄與伺服器的互動狀態
  - session：屬於伺服器，是伺服器單方面紀錄與客戶端的互動狀態


## 相關的 RFC規範 和 參考文獻
[Cambridge Dictionary: session](https://dictionary.cambridge.org/dictionary/english/session)
[RFC2109: HTTP State Management Mechanism](https://tools.ietf.org/html/rfc2109)
[RFC2965: HTTP State Management Mechanism](https://tools.ietf.org/html/rfc2965)
[RFC6265: HTTP State Management Mechanism](https://tools.ietf.org/html/rfc6265)
[淺談 Session 與 Cookie：一起來讀 RFC](https://blog.huli.tw/2019/08/09/session-and-cookie-part2/)