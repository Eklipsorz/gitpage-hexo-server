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
- [What exactly is Socket](https://stackoverflow.com/questions/16233193/what-exactly-is-socket)
- [A TCP/IP Refeerence You Can Understand](http://www.tcpipguide.com/free/t_SessionLayerLayer5.htm)
- [socket编程到底是什么？](https://www.zhihu.com/question/29637351)

### Sequence number 是什麼？
1. 出自於TCP/IP 傳輸層的Sequence number，主要用途為藉由賦予連續號碼至每個所傳遞的封包來對封包進行排序、去除重複性(eliminate duplicates)或者讓每個封包都有各自的序號
2. 細節：
  - Sequence number並不是TCP/IP的識別號碼
  - Sequence number並不代表會如同資料庫的主鍵號碼那樣：每個封包要擁有不同號碼，被使用過的號碼就不能夠重複使用
3. 參考資料：
 - [TCP 序列號 (Sequence Number, SEQ)](https://notfalse.net/26/tcp-seq)
### Acknowledgment number 是什麼？
1. 出自於TCP/IP 傳輸層的Acknowledgment number，主要用途為藉由目前連線的序號來方便讓對方驗證該序號是否為過去錯誤請求的序號，比如因網路速率問題而導致的重複性發送初次連接建立申請。

### ACK? SYN? RST?
1. 出自於TCP/IP 傳輸層 Header的Control bit，該Control bit/TCP Flag本身在Header都用來告知接收方和傳送方該封包內容是屬於哪一種類型或者表明其封包的用途為何
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1631374337/blog/TCP_IP/tcpflag_wumylp.png)

2. 在這裏的ACK、SYN、RST主要用途分別為如下：
  - SYN (Synchronize sequence numbers)：若為1的話，則告知對方 "傳送方想透過序號同步的方式來申請與對方建立初次連接(我方對於對方的初次連接建立請求)，並於封包內的Sequence Number欄位寫入序號"
  - ACK (Acknowledgment field significant)：若為1的話，則告知對方 "接收方已經收到先前傳送方發送過來的封包，並允許對方對於接收方的方向已成功建立連接，接著在封包內的Acknowledgment Number欄位寫入前一個封包的Sequence Number欄位值+1"，**但這不表示就成功接收方對於對方的方向已成功建立連接，只是單方面成功建立了對方對於接收方的方式是成功建立連接**
  - RST (Reset the connection): 若為1的話，則告知對方 "目前我方與你的連線狀態有問題，建議對方刪除我方與你的連線，並重新建立連線"，發生在三向交握的話，會是指接收方所傳遞的ACK欄位值並不是目前傳遞方所擁有的SEQ值+1所發生過期問題，這時傳送方會告知對方目前建立的連線是有問題，請接收方刪除目前連線並重新等待是否有其他SYN封包。
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1631371279/blog/TCP_IP/tcp_ipHeader_rwqwcl.png)


### window size 是什麼？做什麼用？

1. 出自於TCP/IP 四層中的傳輸層 Header，主要用途為宣告接收者在特定時間點所能接收到的最大資料大小，以此來控制流量。
2. 當主機A的封包1會從傳輸層封裝至實體層並傳送至另一個主機B時，其封包1若指定TCP/IP Header中的window size為size A：
  - 那麼當主機B收到封包1時，會從window size讀取到size A，進而指定下一次傳遞封包給主機A的最大封包大小為size A
  - 當主機B傳遞封包2至主機A時，就會按照size A來製作符合其大小的封包傳遞至主機A，另外主機B所傳遞的封包2也指定著window size為size B
  - 當主機A從主機B收取封包時，會從Header讀取到size B，這指定下一次傳遞給主機B的最大封包大小會是size B。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1647839246/blog/network/OSI/window-size-diag_ypolsp.png)


3. 參考資料
 - [What is the window size in TCP?](https://www.quora.com/What-is-the-window-size-in-TCP)
 - [TCP series #4: TCP receive window and everything you need to know about it](https://accedian.com/blog/tcp-receive-window-everything-need-know/)
 - [tcp傳輸窗口解析——藉助wireshark深入分析](https://kknews.cc/zh-tw/news/8ge5n9q.html)


## 什麼是連線？
根據[RFC 793 - Transmission Control Protocol ](https://tools.ietf.org/html/rfc793)以及[为什么 TCP 建立连接需要三次握手](https://draveness.me/whys-the-design-tcp-three-way-handshake/)所提出的連線定義為，連線會是由序號、視窗大小、socket pair所構成。

> The reliability and flow control mechanisms described above require that TCPs initialize and maintain certain status information for each data stream. The combination of this information, including sockets, sequence numbers, and window sizes, is called a connection.

什麼是握手？
四次斷開？
http協定特性
tcp 三次握手侷限於於特定協定？
哪些資訊是定義可靠性
哪些資訊是定義流控制機制
重複歷史連接是什麼？


 - [what is connection](https://datatracker.ietf.org/doc/html/rfc793)
 - [为什么 TCP 建立连接需要三次握手](https://draveness.me/whys-the-design-tcp-three-way-handshake/)
