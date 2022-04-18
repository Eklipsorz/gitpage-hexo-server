---
title: 探討：Cookie 本身和設定
date: 2022-04-19 00:56:48
tags:
  - Cookie
categoires:
  - Website Development
---

由於http/https協議本身為了簡化客戶端和伺服器之間的連線結構，而定義客戶端和伺服器之間在連線時互動情況並不會紀錄下來-無狀態(stateless)，但隨著越來越多服務需要客戶端和伺服器之間的過去互動情況，比如首次登入後就直接透過互動情況而跳過、使用者可以透過過去的購物車內容來直接購買，就有人提出讓客戶端、伺服器根據情況來從自己的系統索要空間(記憶體或者硬碟)來**分別讓客戶端單方面紀錄與伺服器之間的互動情形、讓伺服器單方面紀錄與客戶端之間的互動情形、更或者讓兩者紀錄雙方的互動情形**。

在這裡客戶端單方面與伺服器之間的互動情形紀錄會是稱作為cookie，而伺服器負責單方面與客戶端互動情況紀錄會是session，

## cookie

客戶端的 cookie 是一塊用來記錄與伺服器的過去互動狀態的資料，會根據伺服器的回應是否帶有Set-Cookie才決定是否於客戶端建立cookie，若有的話，就建立；若沒有，就不建立。通常當伺服器想要客戶端儲存目前互動狀態時，伺服器會發送一個擁有Set-Cookie標頭(header)的封包給客戶端，其標頭內容會是以能夠代替這次的互動狀態，其內容形式會是如下(cookie-name和cookie-value即為這次的互動狀態)，客戶端一收到就會根據標頭內容來設置自己的cookie內容為cookie-name:cookie-value，
```
Set-Cookie: <cookie-name>=<cookie-value>; 
```

當客戶端收到之後，就會紀錄伺服器所指定的cookie內容並建立一個cookie，並於每一次向伺服器發送請求封包時，就會在請求封包內附加其cookie內容，形式會是如下
```
Cookie: <cookie-name>=<cookie-value>
```
舉一個例子：假設伺服器設定以下標頭內容的封包並傳送至客戶端，
```
Set-Cookie: food=icecream; flavor=cheese;
```
那麼客戶端收到Set-Cookie標頭封包便會建立cookie這空間來紀錄food、flavor對應的資料及其屬性，並且在每次向伺服器發送封包時，就會附加起司冰淇淋這cookie內容至封包內的cookie標頭中
```
Cookie: food=icecream; flavor=cheese;
```

## cookie存放在哪？
會存放在記憶體或硬碟這兩個空間的其中之一，能夠決定cookie存放在至哪還是得依據每一種瀏覽器所做的實現，不過具體來說，主要會看伺服器對於cookie內容的有效期限是如何:
  - 時限若太短的話，會決定放在記憶體，並直到時間一到或者使用者關閉瀏覽器就即刻從記憶體刪除對應的cookie內容。
  - 時限若太長的話，會決定放在記憶體，直到使用者關閉瀏覽器就會將cookie內容以檔案形式轉存至硬碟，等到瀏覽器再一次向同一個伺服器發送請求時(或者每一次瀏覽器在一次被打開時)，就會先從硬碟中的cookie內容載入至記憶體，並利用其內容來與伺服器交流

參考資料：
[Cookies vs. LocalStorage: What’s the difference?](https://medium.com/swlh/cookies-vs-localstorage-whats-the-difference-d99f0eb09b44)
[HTTP cookies](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Cookies)
## 客戶端的cookie設定

另外由於考量到cookie內容隸屬於哪個伺服器下的哪個位置、cookie本身的安全問題、留存客戶端多久的問題，會允許伺服器端藉由Set-Cookie標頭來設置其Cookie本身的設定，在這伺服器會以option來設定
```
Set-Cookie: <cookie-name>=<cookie-value>; <option>
```


### cookie內容隸屬於哪個伺服器下的哪個位置
每一次客戶端與伺服器的互動狀態若要紀錄的話，勢必得標記互動狀態是哪個客戶端與哪個伺服器進行互動，在這裡是選擇曾互動的客戶端負責紀錄互動狀態，那麼唯一就差在於**這筆狀態是與哪個伺服器進行互動**，為了方便紀錄，會於Cookie內容以Domain來標記這筆狀態是**與哪個伺服器進行互動**，或者準確地來說，伺服器會設定Domain來**告知客戶端目前Cookie內容是隸屬於哪個伺服器**。

```
Set-Cookie: food=icecream; flavor=cheese; Domain=xxxxx
```

當然同一個伺服器也擁有著不同的路徑，若要更仔細紀錄客戶端到底與哪個路徑進行互動，會使用Path來紀錄。
```
Set-Cookie: food=icecream; flavor=cheese; Domain=xxxxx; Path=xxx
```

總結，具體會是以Domain來指定隸屬於哪個伺服器，而Path則是指定Domain下的哪個路徑：
1. Domain：實際標記cookie內容是隸屬於與哪個伺服器下進行互動交流，並且嚴格規範每一次客戶端要附加cookie內容會根據Domain所指定來附加。

比如說伺服器設定cookie內容是與mozilla.org進行交流，那麼每一次客戶端向mozilla.org就會附加起士冰淇淋，若不是mozilla.org就不會附加
  
```
Set-Cookie: food=icecream; flavor=cheese; Domain=mozilla.org 
```

Domain 剩餘細節：
  - 若設定Domain這設定值，會同樣指定cookie內容也同樣綁定於其Domain的subdomain，比如假設mozilla.org的subdomain為developer.mozilla.org，那麼伺服器設定Domain為mozilla.org，其cookie也就同時隸屬於mozilla.org和developer.mozilla.org
  ```
  Set-Cookie: food=icecream; flavor=cheese; Domain=mozilla.org 
  ```
  - 若伺服器未設定Domain這設定值，就預設以當前伺服器的主機域名為主，但不會像前者那樣也讓subdomain也隸屬。

2. Path：指定Domain下的哪個路徑，比如假設以下內容為Set-Cookie標頭內容，那麼
```
Set-Cookie: food=icecream; flavor=cheese; Domain=mozilla.org Path=/docs
```

比如說
```
Set-Cookie
```

#### first-party cookie

若cookie的隸屬網域是與使用者目前所在的網域相同，即為**對於目前所在網域X而言，cookie的隸屬網域就是網域X或者說同樣是伺服器所在的網域，且由於在這裡是以伺服器為提供服務協議上的第一方(詳見註解)，所以故此稱為first-part cookie**

這項定義也**適用於使用者所在網域為cookie的隸屬網域下的子網域**，但僅限於伺服器是主動設定Domain，而不是採用預設值。

舉例來說：首次伺服器同樣發送Set-Cookie標頭設定，那麼其cookie會隸屬於mozilla.org和其子網域developer.mozilla.org，當使用者去瀏覽著developer.mozilla.org或者mozilla.org，不論哪一個，只要伺服器有主動明確規定Domain是mozilla.org，那麼對於mozilla.org和其子網域而言，隸屬於mozilla.org的cookie內容就是first-party cookie

```
 Set-Cookie: food=icecream; flavor=cheese; Domain=mozilla.org
```


#### third-party cookie
若cookie的隸屬網域是與使用者目前所在的網域不同，即為**對於目前所在網域X而言，cookie的隸屬網域就只是外來網域的cookie內容，是第三方來的cookie**，另外沒有第二方cookie是因為第二方本身是指client。

原本理論上是不允許瀏覽器將隸屬於網域X的cookie傳送至網域X以外的網域，但卻允許同個網域X下會有其他網域的服務向客戶端。

舉例來說：假設有兩個伺服器分別為Server1和Server2，而他們的Domain皆為不一樣，也不是parent-child的關係，一開始客戶端Client會向Server1索要網頁，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650275946/blog/network/ClientAndServer/cookie-require_a_website_pscr94.png)

接著客戶端對網頁上進行某種互動(比如登入)，使得客戶端向伺服器發送請求，接著伺服器收到就做完一定的操作，並回應一個封包要求客戶端紀錄cookie內容，並將這次cookie內容設定隸屬在Domain 1，也就是客戶端下的Cookie 1，這時的Cookie 1隸屬域名會因為與客戶端目前所瀏覽的網頁網域是相同的，而Cookie 1會是first-party cookie
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650275945/blog/network/ClientAndServer/cookie-require_a_cookie_q9lbnf.png)

而若原伺服器傳過來的網頁包含著某個插件(add-on)或者程式模組，該插件對應伺服器會是另一個伺服器2，當客戶端執行網頁下的呈現和JS時，會連同執行該插件，而這插件會以客戶端的名義向伺服器2發送請求，伺服器2處理完，隨後就要求客戶端紀錄cookie內容，並且指定該cookie隸屬於伺服器2所在的Domain2，客戶端就便建了隸屬於Cookie 2，但由於目前客戶端實際是瀏覽著Domain 1的網頁，對於Domain 1而言，隸屬于Domain 2的Cookie，就是third-party cookie。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650275945/blog/network/ClientAndServer/cookie-require_a_cookie_from_3rd_g1he6f.png)


在這裡那個插件通常會用作於紀錄使用者在Domain 1下瀏覽了哪些頁面和做了哪些操作，並隨時與伺服器2進行回報，同時將回報處理結果放置在客戶端Cookie2上，每一次插件代替使用者向伺服器2發送請求就會附加客戶端的Cookie 2，好在未來根據結果來投放適合使用者的廣告。通常有網頁存在這樣子的服務，會事先告知使用者第三方 cookies 的存在，以降低發現插件和插件相關的cookie的負面影響，

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650276985/blog/network/ClientAndServer/third-party_cookie_example_wuyvts.png)

Note:
1. first-party vs. second-party vs. third-party： party是指參與者、參與方，源自於合約關係中的甲乙兩個參與方(party)，在這裡將甲方(提供服務的人)視為第一方(first-party)，而乙方(接受服務或者索求的人)視為第二方(second-party)，而排除在合約以外的一方就稱之為第三方(third-party)，然而除了第三方以外，實際上並沒有硬性規定哪一方為第一方和第二方，只是慣例上和傳統上的決定。

參考資料
[Exactly what is a "third party"? (And who are the first and second party?)](https://stackoverflow.com/questions/2895753/exactly-what-is-a-third-party-and-who-are-the-first-and-second-party)



#### 客戶端預設可以跨網域發送請求
由於 **客戶端每當發送請求時，會根據請求的Domain會是什麼而根據Domain來提供對應的cookie至請求封包**，然而這允許位於外來網域的伺服器可以藉由這點來藉由目標伺服器的Domain來偽造對於該伺服器的請求來讓客戶端附加自己對於該Domain的cookie內容，

舉例來說，假設有兩個伺服器分別為Server 1、Server 2，網域皆為不同，一開始客戶端向伺服器1索要網頁並於回應中渲染其網頁，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650290114/blog/network/csrf/csrf-flow-website1-rendering_civqv4.png)

將著當客戶端對著從伺服器1拿到網頁做了一些互動(登入)而對伺服器1發送請求，伺服器1接收到就處理，處理完之後，就會隨之建立session，並通知客戶端建立cookie來紀錄session id，客戶端收到後就便建立專屬於伺服器1所在網域(Domain1)的cookie來儲存，也就是cookie 1

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650285736/blog/network/csrf/csrf-flow-obtain-cookie_jdm1nz.png)

緊接著，客戶端在cookie 1還留存在瀏覽器情況下去向伺服器2索要網頁來渲染，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650285736/blog/network/csrf/csrf-flow-website2-rendering_ucbzsn.png)

接著伺服器2給定的網頁中藏有一個程式模組來讓客戶端在Domain 2的環境下去向伺服器1所在的Domain1發送特定請求，而這請求是由伺服器2利用Domain1這域名來偽造出來的，而當瀏覽器讀取到請求上是包含著Domain 1，就會從cookies中找尋隸屬於Domain 1的cookie並附加至這偽造請求，那麼此時當伺服器讀取到這請求時，會讀取其cookie內容，會以爲是客戶端真的是確實要發送這請求就執行，但實際上是執行伺服器2所偽造的請求，而這也是所謂CSRF(Cross-Site Request Forgery)問題，跨網域請求偽造，若這個請求是涉及金錢、帳號、個人隱私等敏感問題的話，勢必會是很嚴重的問題。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650285736/blog/network/csrf/csrf-flow-request-forgery_my7ghb.png)


### 安全設定
主要option會有：
1. Secure: 要求客戶端只能在https加密協議下才能附加對應cookie給伺服器端，若是沒有就不附加，若有就附加cookie內容
2. HttpOnly: 要求只能透過解析http封包本身的標頭才能讀取到cookie內容，除此之外的方法皆無法被存取，也不能透過JavaScript讀取，如透過Document.cookie來讀取和變更
> Forbids JavaScript from accessing the cookie, for example, through the Document.cookie property
3. SameSite：是否讓cookie遵從同源政策(當目前請求上的域名、路徑名、埠號是與cookie所歸屬的域名、路徑、埠號一致認同的話，就允許cookie加入至請求)，這是為了要求客戶端不能跨網域(Domain)來向指定網域發送請求之相關設定，也就是客戶端不能夠在非指定網域下發送請求，主要的選項有None、Strict、Lax，
  None: 允許客戶端可以跨網域來向指定網域發送請求。
  Strict: 不允許客戶端可以跨網域來向指定網域發送請求。
  Lax: 如同字面上的意思，不會全然禁止客戶端跨網域向指定網域發送，而是只限定部分請求和元件，比如a元素的連接功能、get方法的表單。

### 留存客戶端多久

## 參考資料
[一篇解释清楚Cookie是什么？](https://learn-anything.cn/http-cookie)
[網站安全🔒 再探同源政策，談 SameSite 設定對 Cookie 的影響與注意事項](https://medium.com/程式猿吃香蕉/再探同源政策-談-samesite-設定對-cookie-的影響與注意事項-6195d10d4441)
[广告是如何跟踪我们的？所有关于 cookie](https://juejin.cn/post/7052507369690890270)
[傻傻分不清之 Cookie、Session、Token、JWT](https://www.readfog.com/a/1632001918648815616)
[同源政策 (Same-origin policy)](https://developer.mozilla.org/zh-TW/docs/Web/Security/Same-origin_policy)
[零基礎資安系列（三）-網站安全三本柱（Secure & SameSite & HttpOnly）](https://tech-blog.cymetrics.io/posts/jo/zerobased-secure-samesite-httponly/)
[Cookie起源与发展](https://zhuanlan.zhihu.com/p/74042200)
