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

然而 session 僅僅只是描述著 **伺服器和客戶端之間在連線時的互動情形** 這抽象概念，而非代表存在著能夠代表互動情形的資訊並儲存在相關的伺服器和客戶端，換言之，session在這裡目前是毫無任何形式來表現，比如沒用資訊這具體形式來表示session，就只是一段話來描述。

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

具體實作方式為每一次只要客戶端Client和伺服器Server之間建立連線，且客戶端Client向伺服器Server發送請求，那麼伺服器Server就會為了該客戶端在同一個連線紀錄互動狀態而建立名為session物件來代表伺服器Server和客戶端Client之間連線時的互動要開始了，並準備儲存代表session(伺服器和客戶端之間在連線時的互動情形，不是指名為session的物件)的資訊
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650439921/blog/network/session/building_a_session_ommsjq.png)

接著伺服器就會回傳Set-Cookie標頭的封包，其標頭含著sessionId和name、value所構成的資訊內容，sessionId是用來告知客戶端哪一個伺服器session是紀錄著與客戶端Client連線時的互動狀態，好在未來方便伺服器和客戶端交換資訊，而資訊內容則是伺服器要求客戶端儲存的內容-即為讓客戶端單方面紀錄與伺服器連線的互動狀態，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650439921/blog/network/session/session_response_jybm8b.png)

最後當客戶端收到之後就便建立Cookie物件來紀錄sessionId和對應資訊內容，往後若客戶端和伺服器都處於同個連線下，若發生互動交流的話(客戶端將產品放進購物車、使用者登入)，兩者皆透過Cookie物件和session物件來交換資訊來處理
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650439922/blog/network/session/building_a_cookie_bujrln.png)

## 總結
1. session 本就是以抽象概念來描述 **伺服器和客戶端之間在連線時的互動情形**，而 stateful session 則是利用 **將互動情形轉換以具體形式來描述情形，並讓每一個session都能擁有過去的session來進行處理**
2. stateful session概念的存在意義是為了 **在不違反http無狀態下的環境能夠滿足伺服器和客戶端對於狀態的需求**
3. 在實現stateful session概念下，客戶端和伺服器會有特定空間或者特定物件來儲存伺服器和客戶端之間在連線時的互動情形，這些空間或者物件都有對應名稱：
  - Cookie：屬於客戶端，是伺服器麻煩客戶端去紀錄與伺服器的互動狀態
  - session：屬於伺服器，是伺服器單方面紀錄與客戶端的互動狀態


## 相關的 RFC規範 和 參考文獻
[Cambridge Dictionary: session](https://dictionary.cambridge.org/dictionary/english/session)
[RFC2109: HTTP State Management Mechanism](https://tools.ietf.org/html/rfc2109)
[RFC2965: HTTP State Management Mechanism](https://tools.ietf.org/html/rfc2965)
[RFC6265: HTTP State Management Mechanism](https://tools.ietf.org/html/rfc6265)
[淺談 Session 與 Cookie：一起來讀 RFC](https://blog.huli.tw/2019/08/09/session-and-cookie-part2/)