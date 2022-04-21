---
title: 筆記&探討：Session 本身和設定
date: 2022-04-21 23:23:19
tags:
  - Cookie
  - Session
categories:
  - Website Development
---

鑑於 [探討：http 協議上的 session 概念](/session-concept-in-rfc)所提到stateful session的概念，在這裡會基於概念的實現來說明session，此外，客戶端的cookie和伺服器的session皆為 **實作stateful session概念的產物**，不過這裡只會探討到實現層面，並且為了特別區分出哪一個是實作以及哪一個才是session概念，會特別強調session物件來表示實作

## 伺服器的session

cookie放在客戶端上，會是由瀏覽器負責實現其概念和如何管理cookie，而伺服器上的session則是依據基於RFC規範的第三方套件來負責產生session物件來實現session概念、使用名為Session store的空間來集中管理著這些session物件、每個session物件本身的設定(如安全、持續時間)，所以實際上session物件本身的好壞會由第三方套件來決定

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650528404/blog/network/session/session-example_broxec.png)

通常情況下，每個session物件是由key-value所組成的hash table結構(或者近似於hash table結構)，每一個session都有對應的key值或者使用session ID作為key值，其value會是該物件實際存下的內容-與客戶端連線時的互動狀態


在客戶端與伺服器端建立連線後，客戶端就會向伺服器發送請求，伺服器收到請求，隨後為了紀錄請求和回應的過程而建立了名為session的物件來儲存過程中所會有的資訊(PS.這些資訊正是代表著連線時的互動狀況)，而這個物件實際上會佔用伺服器的記憶體、硬碟、資料庫系統空間(PS. 該空間也會從記憶體或者硬碟兩者之一獲取)。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650439921/blog/network/session/building_a_session_ommsjq.png)

## 存了哪些資訊
具體來說，session概念本就是為了要讓伺服器能藉由過去(客戶端和伺服器之間)連線時的互動狀態來實現特定功能回應給客戶端，在這裡，為了讓伺服器實現特定功能，客戶端利用cookie物件單方面儲存與伺服器之間連線時的狀態，而伺服器利用session物件則是單方面儲存與客戶端之間連線時的狀態。

然而由於客戶端的cookie物件所存的資料易讓惡意使用者讀取使用更或者儲存的資料過於龐大，所以會委託專門處理資料的伺服器端來幫忙儲存cookie物件的原始資料，所以實質上，session主要會儲存的內容有：
1. 代替客戶端cookie那一端來所儲存較為龐大或者較為敏感的資料
2. 延續第二點，但不同的是伺服器委託其他系統X來儲存cookie的原始資料，並讓伺服器的session物件儲存原始資料位於系統X的索引所在
3. 除了委託用途以外，伺服器實現特定功能所需要的參數內容

第一點所提到的資料具體來說，由於客戶端的cookie本身受限於沒辦法放多資料且因面向前端而容易遭受到
惡意使用者讀取並使用，所以才會選擇將資料改放在專門處理資料的伺服器上，且比起客戶端而言，比較不容易受到惡意使用者從外部惡意讀取到資料，通常伺服器的實現上會直接存放cookie的原始資料，如下圖中的Session那樣儲存cookie的原始資料

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650529849/blog/network/session/data-inside-session_lxazph.png)


最後客戶端每一次要利用cookie原始資料與伺服器互動時，只需要向伺服器發送請求時附加原始資料位於session store的索引位置，當伺服器收到請求便會讀取索引位置來找到對應的session讀取客戶端委託伺服器儲存的原始資料，接著就是利用者原始資料處理業務，最後回傳結果給客戶端
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650531966/blog/network/session/index-inside-cookie-example1_mscr6s.png)


第二點是延續第一點，伺服器學客戶端那樣委託其他系統X來儲存cookie上的原始資料，然後再由session物件儲存原始資料於其他系統X的索引位置，這系統X的型態會是使用資料庫的某一個區塊或者檔案本身來儲存cookie的原始資料，如下圖中的左右兩個例子：左邊是將原始資料以檔案形式儲存在硬碟，並讓session儲存原始資料位於在硬碟的索引位址，右邊是將原始資料以紀錄或者符合資料庫表格格式的形式儲存在伺服器，並讓session儲存原始資料位於在資料庫的索引位址。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650529850/blog/network/session/index-inside-session_op9oon.png)

最後同樣地，客戶端每一次要利用cookie原始資料與伺服器互動時，只需要向伺服器發送請求時附加原始資料位於session store的索引位置，伺服器收到請求封包中的索引位置來找到對應session，若伺服器還委託資料庫系統來儲存cookie原始資料的話，那麼session的內容勢必對應著資料庫區塊的位址，到此時，伺服器會利用資料庫區塊的位址向對應資料庫系統發送讀取對應資料的請求，麻煩它回傳放在資料庫系統的cookie原始資料至伺服器，而資料庫系統讀取到這請求就回傳資料，獲取到原始資料的伺服器就直接處理資料並回傳結果至客戶端。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650533730/blog/network/session/index-inside-cookie-example2_dgmqhf.png)

但若伺服器委託的系統並不是資料庫的話，而是處於硬碟下的檔案，那麼就其session內容勢必對應著硬碟位址，到此時，伺服器會利用硬碟位址來向對應的檔案系統發送讀取原始資料的請求，其系統收到後便回傳資料至伺服器本身，最後伺服器獲取原始資料就便處理，隨後回傳結果至客戶端。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650533729/blog/network/session/index-inside-cookie-example3_gfegar.png)

另外在這裡也可以將硬碟替換成記憶體來儲存cookie原始資料，只是與持續性來說，記憶體雖可以憑藉記憶體階層以及本身效能來達到快速存取的，但如果途中客戶端和伺服器的連線中斷，那麼很有可能因爲為了節省記憶體而直接釋放掉，或者說整體應用程式因爲崩潰被迫重開，導致記憶體被刷新；硬碟在這替代的話，則可以免除這問題，只是會因為記憶體階層以及本身效能不像記憶體那樣而無法達到快速存取的效果。

第三點所提到的儲存內容具體會是為了伺服器本身實現特定功能下所需的參數內容，在這裡伺服器所要儲存的參數也可以存在資料庫、檔案系統、記憶體等等，而session內容就直接指向於原始資料在這些系統中的位址。

### 安全問題：偽造session-id
當客戶端的cookie物件內容來使用session id 來告知伺服器哪一個伺服器的session 物件內容是要拿來處理這次的session時，若session id被篡改成其他客戶端與伺服器之間的session物件，那伺服器該如何防範？

在這裡的解法會是，當每次客戶端發出請求致使伺服器建立session時，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650553317/blog/network/session/create-session-sign_mjqbsd.png)

原本伺服器會藉由Set-Cookie標頭封包來告知客戶端對應的sessionID是什麼(圖中會是1)，但在這裡會選擇將sessionID和伺服器所儲存的密鑰(secret)一起透過雜湊演算法來產生名為簽名(signature)的雜湊值，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650553443/blog/network/session/sign-generate_grxsdm.png)

並將傳遞給客戶端的封包改成以下形式的字串，在這裡會是1.signature1，其中signature1是透過sessionID(=1)和密鑰而構成的，而當客戶端收到之後，就便建立cookie物件來存放sessionID和簽署所構成的字串。
```
Set-Cookie: sessionId=<session id>.<signature>
```

而當下次客戶端再次向同個伺服器發出請求時，便會把對應的cookie內容(此時會是1.signature1)附加至請求封包，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650553317/blog/network/session/send-with-cookie-sign_n2hv2s.png)

而伺服器收到之後就會分別對sessionID和signature1進行處理，首先會先對收到的sessionID和伺服器的密鑰進行雜湊演算法獲取另一個簽名(signature')，並且將結果簽名值與接收過來的signature比較是否相同，若相同就代表著接收到的封包內容是未被竄改，未被竄改才會允許伺服器去找尋對應的session，否則就駁回
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650553936/blog/network/session/sign-compare_hfxe4p.png)

在這裡除非接收封包內的sessionID和signature都被改成相對應的組合(PS. sessionID透過同個算法和密鑰能夠得到同樣的signature)以外，否則只要任一改變就會被認定被惡意竄改而拒絕。此外，事實上這方法也藉由讓客戶端儲存要比較的結果簽名值來減少伺服器對空間需求。


## 儲存在哪？ && 何時釋放？
伺服器的session物件主要放置於名為session store的空間，該空間通常會是由記憶體或者硬碟所構成，至於session物件的持續留在伺服器的時間就決定了何時釋放該物件所佔用的記憶體空間，然而這部分得依據實際的第三方套件來決定。

若是由express-session套件內建預設的MemoryStore模組來製作和管理session物件，其會在記憶體上分配空間來設置store並從中建立session物件來儲存每一次與客戶端的互動狀態，另外MemoryStore模組本身只是為了測試session而開發，本身不會監測每個session的狀態來進行記憶體釋放或者管理，會有記憶體洩漏問題-**某一些記憶體區塊無法被正常釋放，而這些記憶體區塊還保留值並且不被系統看待成可用的記憶體，造成記憶體上的浪費**，所以MemoryStore模組並不適合真正的上線後環境
> Warning The default server-side session storage, MemoryStore, is purposely not designed for a production environment. It will leak memory under most conditions, does not scale past a single process, and is meant for debugging and developing.

不過還是有擅長管理每個session的狀態以及記憶體釋放的第三方套件，比如說：
  - 以硬碟為主的資料庫來管理session：MongoDB
  - 以記憶體為主的資料庫來管理session：Redis
  - 以記憶體空間來管理session：Memcached

參考資料：
[一次 node.js 内存泄漏排查](https://zhuanlan.zhihu.com/p/257854926)


## 誤解 : 
1. 客戶端的cookie刪了，session就跟著刪了？
通常來說，客戶端cookie若突然主動被刪去，並不會跟著刪除伺服器下的session物件，只不過會由於cookie儲存的session id被刪去，而導致無法讓伺服器找到對應的session，而產生伺服器的session物件被刪去之錯覺，不過除非說cookie和session這兩者的過期時間都是一樣的，那麼就有可能會一同在同一時間被刪去。