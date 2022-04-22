---
title: 前後端分離：客戶端如何存API Token？
date: 2022-04-23 02:49:45
tags:
  - Cookie
  - Bearer Token
categories:
  - Website Development
  - Authentication
---

當前端成功從API伺服器獲取API Token時，往後若要使用API伺服器獲取特定服務必須將對應Token傳給伺服器以此證明自己是合法使用者來獲得使用服務，而客戶端勢必要有一個空間要能夠存放API Token，主要有兩種方式：
  - 使用cookie來存放API Token：由於前後端分離出來，所以會容易因為兩者所處的Domain不同，所以容易因為同源政策關係，而無法從隸屬於後端伺服器的cookie取出token來從目前所在的網域發出合法的請求
  - 使用Bearer Token：將Token放在HTTP請求封包上的標頭，尤其是Authorization標頭

在這裡我們以兩個例子來簡介說明這兩個方法，同時以下面為前面來敘述：
```
客戶端的Cookie會是以Secure、httpOnly、SameSite(=strict)
伺服器端設定CORS的相關設定來允許客戶端可透過FETCH/XHR方法來存取API伺服器
```

## 使用cookie來存放API Token
若客戶端順利從位於Domain1的伺服器獲取網頁，那麼就代表著此時客戶端獲取到位於Domain1的網頁，每一次客戶端只要在這網頁上發送請求，就會從這網域發出請求。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650644680/blog/network/api-token/token-in-cookie-example1_cdxloo.png)

並且打算向API 伺服器來獲取對應API Token，API伺服器為了讓客戶端能夠將Token儲存在Cookie，會透過Set-Cookie標頭來設定對應API Token、伺服器的Domain(該Domain為Domain 2)、伺服器的PORT、伺服器的Schema，而當客戶端讀取到的時候，就會按照標頭指示來建立隸屬於該伺服器的DOMAIN(含PORT、Schema)的Cookie來儲存對應的Token。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650644680/blog/network/api-token/token-in-cookie-example2_i0xnis.png)

接下來若客戶端對Domain1 的網頁進行互動而讓網頁想去獲取位於Domain 2的API 伺服器，那麼此時客戶端向Domain 2的API 伺服器發送請求將會是從Domain 1發送，然而客戶端和位於Domain 1的網頁這舉動就違背Cookie上的Same Site Policy而被無法直接使用客戶端上隸屬於Domain 2的Cookie，並且也無法將裡頭的Token附加至發送至API伺服器的請求，而伺服器收到時發現沒對應Token就直接告知對方請重新獲取Token再來發送請求
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650645728/blog/network/api-token/token-in-cookie-example3_svhsie.png)

當然為了解決這樣子的問題，我們可以選擇放棄Same Site Policy而設定Cookie那邊的建立設定為SameSite(=None)，但這樣就會讓伺服器陷入CSRF攻擊的疑慮。


## 使用Bearer Token
我們繼續沿用著上一個小節所使用的例子以及前提，若客戶端不用Cookie儲存API Token，可以透過Bearer Token的形式來將Token來放入客戶端的某塊暫存空間，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650648954/blog/network/api-token/bearer_token_example1_r1jymv.png)

接下來若客戶端對Domain1 的網頁進行互動而讓網頁想去獲取位於Domain 2的API 伺服器時，那麼此時客戶端向Domain 2的API 伺服器發送請求將會是從Domain 1發送，在這裡客戶端從暫存空間取得API token，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1650648954/blog/network/api-token/bearer_token_example2_ibn7nw.png)

來將 **將要發出的請求添加API Token至Authorization Header** ，形式會是如下，其中Bearer只不過是告知伺服器這是個Bearer 種類的token(實際上還有Basic Token)，請按照Bearer Type來回應，接著當伺服器收到請求封包並且取出Bearer Token來進行相關驗證(如比對是否被竄改)，若驗證成功，就會准許發出請求的客戶端使用資源，若驗證失敗，就會駁回。
```
Authorization: Bearer <token>
```

### 補充知識：Bearer Token

#### 名字緣由

Bearer Token原本取自於銀行界的可兌換支票，其中Bearer是指持票人或者拿著支票的人，Token是指支票本身，合在一起就是強調著 **代表持票人** 的支票，在這裡銀行業務員只要一看到任何一個使用者X拿到這張支票，就會認定那個就是合法的持票人，並且按照支票所指示的金額或者所要提供的服務而將銀行內部資源提供給使用者X，換言之，誰擁有著Token，誰就擁有權利獲取應該要有的資源，完全不看到使用者到底是不是合法的使用者。 Bearer Token因此這樣子的特性而因為不看使用者是誰而被別名為不記名憑證。

源自於[bearer token到底是什么？](https://www.zhihu.com/question/305585277)
```
最近研究第三方支付系统接入，设计中有许多设计到Bearer的概念，我觉得Bearer Token的设计概念也应当与类似支票兑付的场景模型有关，我来说一点自己的理解。字面翻译Bearer，指的是持票人，用支票使用场景来解释的话，就是指手持支票坐等兑付的受款人（这里假定是最普通的银行汇票）。那么银行就作为承兑人，以票面上出票人的签名为凭证行使保障受款人收款的义务。

转回Bearer token的场景，这里的“票”就指的是token。token作为一种凭证，server端必须承认持有token的用户拥有token用户的对应权利，此时持有token的用户就是一个“bearer”，应当作为“受款人”获取server的“付款”，即可以访问token所允许的资源。所以由此来看，所谓Bearer token，其实就是token。毕竟token本身就是令牌的意思，表示持有者拥有了权限，可以以此享受权利，获取资源。（当然也不排除Token在其他场景具备其他使命，毕竟互联网使用场景千千万）当然，我还是觉得Bearer token这种“Bearer”多余了，造成不必要的理解负担。
```

#### Bearer Token 還有兄弟姐妹？
其實將token 封裝至請求封包之Authorization標頭的形式主要分為Basic Type和Bearer Type，而由於封裝至請求封包的token內容也會跟著變動，所以可以稱之為Basic Token和Bearer Token。

Basic Token主要是將帳號和密碼這些可以被視為credential的資料以base64url編碼或者base64編碼來製作token，並直接放至請求封包上的Authorization標頭，然而實際上base64編碼可以被反解，因此在某種程度上是以明文來放置在Authorization標頭，並且很容易被人惡意使用

```
Authorization: Basic <token>
```
而Bearer Token(別名稱作為Token authentication)概念為 **誰擁有這Token，誰就有Token對應的資源使用權利和權益** 為主，而非像Basic Token那樣，主要會驗證使用者本身是誰。
```
Authorization: Bearer <token>
```
[Web API Authentication Basic vs Bearer](https://stackoverflow.com/questions/34013299/web-api-authentication-basic-vs-bearer)

[HTTP Token 使用方式: Basic Token v.s Bearer Token](https://ithelp.ithome.com.tw/articles/10256445)

## 參考資料

[bearer token到底是什么？](https://www.zhihu.com/question/305585277)
[What's the difference between JWTs and Bearer Token?](https://stackoverflow.com/questions/40375508/whats-the-difference-between-jwts-and-bearer-token)
[https://www.zhihu.com/question/305585277](https://www.zhihu.com/question/305585277)
[Web API Authentication Basic vs Bearer](https://stackoverflow.com/questions/34013299/web-api-authentication-basic-vs-bearer)
[HTTP Token 使用方式: Basic Token v.s Bearer Token](https://ithelp.ithome.com.tw/articles/10256445)

