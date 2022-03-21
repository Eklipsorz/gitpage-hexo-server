---
title: 什麼是 TCP 三向交握？
date: 2022-03-20 17:45:22
tags:
  - three-way-handshake
categories:
  - TCP/IP
---
## 前置知識
### Socket 是什麼？
1. 原文是指某種裝置的插槽，作用是提供介面來給予其他裝置插入該插槽獲取對應功能
> an opening or hollow that forms a holder for something
2. 在電腦科學裡，是指：
  - 由軟體形式所構成的插槽，該插槽會以目前插槽所在的網域和特定埠號等資訊來識別，其對應功能為與網路連接的功能，該插槽可由應用程式透過插入該插槽這類比概念來將應用程式與插槽綁定，使應用程式擁有能夠與網路連接的能力
  - Process 透過網路來進行通訊的方式之一

3. 其概念定義源自於OSI七層中的會話層(Session Layer)，主要目的透過建立一個抽象資料結構(該結構即為Session)來讓應用程式的開發只需要顧慮到會話層透來快速與另一個端點下的應用程式建立連接，而不需要顧慮到底層(傳輸層、網路層、資料鏈結層、實體層)，該資料結構會封裝著底層資訊和對應技術。

4. 在這裏使用了Socket這資料結構來當作Session的形式之一，主要定義以下課題：
  - 如何建立Socket：由作業系統來提供Socket相關的API給予應用程式建立和管理，一旦應用程式呼叫對應API來建立Socket時，其Socket形式會是以檔案形式來表示，並以應用程式目前所在的主機網域和使用埠號作為識別
  - 如何透過Socket傳遞/接收訊息：只要讓應用程式以檔案形式來對該Socket進行寫入/讀取檔案操作就能讓作業系統幫忙封裝底層網路資訊進行端點下的應用程式之間的訊息傳遞或者接收訊息
  > A socket is an abstraction through which an application may send and receive data,in much the same way as an open file allows an application to read and write data to stable storage. A socket allows an application to "plug in" to the network and communicate with other applications that are also plugged in to the same network. Information written to the socket by an application on one machine can be read by an application on a different machine, and vice versa.
5. 例子：在這裡會有兩個主機分別為Host A、Host B，每個主機分別有Application A1和Application B1這兩個應用程式在運作，這兩個都想與網路連接並與彼此傳遞/接收訊息，在這裡主要會以下步驟：
  - 每台主機透過作業系統所提供Socket的API來建立對應的Socket來讓該應用程式與網路連接，每個Socket都用Socket所在的IP和特定Port當作識別用的資訊
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1647832161/blog/network/OSI/build-socket-example_ftpefh.png)
  - 接著使用A1使用API來指定與Host B的B1進行連接，並且當連接建立完畢後，接著透過API來將傳遞內容寫入至Socket A1這檔案，Host A上的作業系統會利用封裝後的底層資訊來將檔案內容傳遞至Host B的Socket B1上，並通知該Host B的作業系統去讀取Socket B1，接著Host B的作業系統收到通知就會讀取Socket B1並將內容轉換給Application B1上。
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1647831617/blog/network/OSI/send-receive-data-inside-socket-example_ypmu7m.png)
6. 參考資料：
[What exactly is Socket](https://stackoverflow.com/questions/16233193/what-exactly-is-socket)
[A TCP/IP Refeerence You Can Understand](http://www.tcpipguide.com/free/t_SessionLayerLayer5.htm)

window size 是什麼？做什麼用？
sequence number是什麼？
什麼是連線？
什麼是握手？
四次斷開？
http協定特性
tcp 三次握手侷限於於特定協定？
哪些資訊是定義可靠性
哪些資訊是定義流控制機制
重複歷史連接是什麼？


 - [what is connection](https://datatracker.ietf.org/doc/html/rfc793)
 - [为什么 TCP 建立连接需要三次握手](https://draveness.me/whys-the-design-tcp-three-way-handshake/)
