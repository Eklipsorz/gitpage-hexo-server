---
title: 什麼是 TCP 三向交握？
date: 2022-03-20 17:45:22
tags:
  - three-way-handshake
categories:
  - TCP/IP
---

## socket 是什麼 ? 包含了什麼？ 
1. 原文是指某種裝置的插槽，作用是用來與其他裝置相互連接並傳遞/接收訊號
> an opening or hollow that forms a holder for something





2. 在電腦科學裡，會
  - Process 之間的通訊方式，即透過網路來進行通訊
  - 作業系統為應用程式所提供的API，目的在於盡可能讓應用程式透過此API來快速與另一個應用程式建立連接，而不需要顧慮到底層(傳輸層、網路層、資料鏈結層、實體層)
  - 此API會定義一個socket 抽象層或者由代表socket的檔案來讓應用程式透過寫入/讀取檔案操作來藉此與另一個
  > A socket is an abstraction through which an application may send and receive data,in much the same way as an open file allows an application to read and write data to stable storage. A socket allows an application to "plug in" to the network and communicate with other applications that are also plugged in to the same network. Information written to the socket by an application on one machine can be read by an application on a different machine, and vice versa.
 1. 原文是指某種裝置的插槽，作用是用來與其他裝置相互連接並傳遞/接收訊號
 2. 在電腦科學裡，會是指電腦網路中的網路節點所具有的軟體結構，其結構會如同字面上的意思而是個插槽，並允許其他裝置透過插槽連接來相互傳遞/接收訊息，插槽間會有實體線路或者無線信號當做媒介來傳輸彼此間的資訊。
 3. 每台電腦都代表著網路節點，換言之，這些電腦上具有一些由軟體建立出來的插槽，插槽之間會透過實體線/無線信號來相互連接，而虛構出來的插槽裝置會負責從中接收和傳送資料
 4. 由於插槽實際上實現是主機之間的網路連接，而主機本身也允許提供額外的應用層的服務，只是若放在同一個插槽勢必多耗成本來檢查每一個接收進來的封包，為此每個服務都會有各自的網路插槽來與其他裝置來連接，以此辨明封包上的種類，並且每個封包按照服務種類來往特定插槽來處理
 5. 由於前者，每個虛構出來的網路插槽都具有port和ip來綁定以此辨明插槽是屬於哪個主機以及主機上哪個服務上。

參考資料：
[What exactly is Socket](https://stackoverflow.com/questions/16233193/what-exactly-is-socket)
[A TCP/IP Refeerence You Can Understand](http://www.tcpipguide.com/free/t_SessionLayerLayer5.htm)

socket ? 包含了什麼？ 
網路七層在做什麼
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
