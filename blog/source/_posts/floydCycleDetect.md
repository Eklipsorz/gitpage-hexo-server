---
title: '(施工中)Introduction: Floyd Cycle Detection'
date: 2020-07-21 15:05:34 
tags: 
 - Algorithm
 - Math
 - Python
mathjax: true
---


當你想解決任何一個需要檢測在多個相互連接的元素是否存在著循環結構之場景，比如說


1. 道路模型。
2. 由多個有限狀態所組成的數學模型。
3. 有限輸入下在同一個函式{% mathjax %}f(x){% endmathjax %}所形成的結果，比如集合為{% mathjax %}\{1,2\}{% endmathjax %}，且{% mathjax %}f(1)=2{% endmathjax %}以及{% mathjax %}f(2)=1{% endmathjax %}，在這裏1和2就透過函
式關係形成一個循環結構。

我們可以將這些場景轉化成由多個節點構成的List結構，並且大致區分為兩種不同結構：
1. 擁有循環結構的List結構 
2. 沒擁有循環結構的List結構


當我們轉換成如此的結構時，我們可以更容易以肉眼看出哪些模型存在著循環，在這裏我們可以知道List A是存在著循環，而List B由於尾巴部分並未跟前幾個節點相接，所以不構成循環。在這裡你或許會選擇以肉眼來辨識，但現實是當面對大量或者複雜的模型時，肉眼看會顯得效率太差，所以最好由電腦進行這樣的重複辨識工作。


可換作是電腦，它要如何辨識呢？畢竟他本身就不存在像人眼那樣的辨識模型，在這裏提供一個方法來幫助電腦辨識：Floyd's Cycle Detection Algorithm， 據說是由Robert W. Floyd所發明的演算法，所以以他的名字來命名，普遍上會以演算法的特色來稱呼：龜兔賽跑算法。顧名思義，這個演算法會假設一隻烏龜和 一隻兔子在這個許多節點構成的List結構進行賽跑，烏龜每次只能走一個節點，而兔子只能走二個節點，如果List結構存在著循環，他們只要跑下去肯定能到循環裡，並且他們肯定能在循環中碰面或者在同一點會合的話。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238046/Algorithm/FloydCycleDetect/CycleExample_thhrj4.png)

然而，如果兔子走到結構中的終點卻沒跟烏龜會合的話，那就表示著結構不存著循環。(如下圖)

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238048/Algorithm/FloydCycleDetect/NoCycleExample_vnsu18.png)


乍看之下這方法很簡單，但問題是這方法真能判別循環問題嗎？如果你對此也感到懷疑，歡迎到Proof章節來進行討論，但如果沒有的話，我可以告訴你 這方法確實能判別循環問題，而非是運氣，另外也建議讀者您參考Implementation以及Performance這兩個章節來看其代碼以及成本。



## Proof:How it works?

首先我們先來考慮擁有循環的結構(如下圖)，在循環之前可能會有{% mathjax %}N{% endmathjax %}個點或者沒存在任何節點，而這{% mathjax %}N{% endmathjax %}的值會影響著烏龜和兔子在循環中的初始位置，再來為了很好地地瞭解影響，設定了數字來表示循環中的第幾個節點，以{% mathjax %}0{% endmathjax %}到{% mathjax %}\lambda-1{% endmathjax %}來命名，而{% mathjax %}\lambda{% endmathjax %}則是定義成循環中的長度，在這裡{% mathjax %}\lambda=10{% endmathjax %}。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238048/Algorithm/FloydCycleDetect/NandCycle_jisqwr.png)

首先我們先考慮著{% mathjax %}N=0{% endmathjax %}時，兔子和烏龜會在循環的起點會合，並從那裡開始進行他們的賽跑。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238046/Algorithm/FloydCycleDetect/N0andCycle_hn1hcj.png)

根據兔子走兩步和烏龜走一步的前提，當兔子走完一圈時，烏龜才走半圈，而兔子再走完下一個完整的圈時，這時烏龜才走完一圈，此時他們倆就在一開始的點會合。

在這裡，我們會發現幾個有趣的觀察結果：

1. 兔子得走完一圈才有辦法跟烏龜會合(p.s 他們倆不動也能會合XD，但這不是在該方法的討論範圍內)
2. 兔子{% mathjax %}H{% endmathjax %}的步數會是烏龜{% mathjax %}T{% endmathjax %}的步數之一倍，換言之，{% mathjax %}H=2T{% endmathjax %}。
3.當兔子{% mathjax %}H{% endmathjax %}和烏龜{% mathjax %}T{% endmathjax %}都走到循環內部時，我們可以對{% mathjax %}H{% endmathjax %}和烏龜{% mathjax %}T{% endmathjax %}使用同餘({% mathjax %}mod\ \lambda{% endmathjax %})的概念(如下式)來 確定是否存在循環，若兩者的餘數都一樣那就表示存在著循環；反之，就是不存在。

{% mathjax %} H ≡ T\ (mod\ \lambda) {% endmathjax %}

將第二個觀察結果納入至{% mathjax %} H ≡ T\ (mod\ \lambda) {% endmathjax %}便會是如下式：

{% mathjax %}2T ≡ T\ (mod\ \lambda){% endmathjax %}

統整這三個觀察結果，我們會發現只要{% mathjax %}T=\lambda{% endmathjax %} 代入上式，兔子和烏龜會在第{% mathjax %}0{% endmathjax %}個節點會合。接下來我們思考另一種情況，如果{% mathjax %}N=1{% endmathjax %}時，這種代入結果會不會有所不同？

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238047/Algorithm/FloydCycleDetect/N1andCycle_ttrcay.png)

從上圖可以觀察出當烏龜進入循環時的位置跟兔子所在的位置是不同的：兩者相差一個節點，這對於{% mathjax %}N=0{% endmathjax %}所得出的觀察結果而言，我們可以確定兔子還是得走完一圈才有辦法和烏龜在同一點，而第二、三個觀察結果可能會因為這樣而改變。

當烏龜進入循環的起點時，兔子在循環中的位置會變成({% mathjax %}H'{% endmathjax %}為循環中的新位置，{% mathjax %}H{% endmathjax %}為循環中的舊位置)：

{% mathjax %}H' = H + 1{% endmathjax %}

接著第三個觀察結果會因循環外的節點增加而改變成下式：

{% mathjax %}H + 1 ≡ T\ (mod\ \lambda){% endmathjax %}

在兩者移動的過程中，仍然依照烏龜每走一步，兔子就會走兩步這前提，只是現在兔子比起原本多走了一步，所以我們可以將上式更改成：

{% mathjax %}2T + 1 ≡ T\ (mod\ \lambda){% endmathjax %}

你會發現這與{% mathjax %}N=0{% endmathjax %}所發現的第二、三個觀察結果有些出入，在這裡第二個觀察結果會變成{% mathjax %}H'=2T+1{% endmathjax %}，而第三個觀察結果就是上式。

那麼式子的改變會不會影響與烏龜會合的情況呢？其實只要我們按照圖上位置來模擬他們移動，最後會發現他們的確會在同一點會合，只是位置變成第{% mathjax %}\lambda-1{% endmathjax %}個位置，在這裡會是循環中編號9的位置，也就是說上式要達到同餘的效果就只有兩者都走到第九個位置(在這裡我們先假定式子的同餘結果會是{% mathjax %}9{% endmathjax %}，後續推理到{% mathjax %}N=M{% endmathjax %}時來驗證)，接著我們再來思考一下{% mathjax %}N=2{% endmathjax %}時，會有什麼樣的變化

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238047/Algorithm/FloydCycleDetect/N2andCycle_lnxwod.png)

同樣的，由於只是單純增加循環外面的節點，同樣地，由於位置因爲循環外的節點增加而改變第二、三個觀察結果，此時兔子的新位置會是：

{% mathjax %}H'=H+2{% endmathjax %}

而第二個觀察結果會變成是{% mathjax %}H'=2T+2{% endmathjax %}，第三個觀察結果就變成是

{% mathjax %}2T+2 ≡ T\ (mod\ \lambda){% endmathjax %}

接著我們只要按照圖上位置來模擬他們移動就會發現他們的確也是會在同一點上會合，但這次是第{% mathjax %}\lambda-2{% endmathjax %}個節點或者第8個節點上會合，如果考慮成{% mathjax %}N=3{% endmathjax %}時，會發現會在第{% mathjax %}\lambda-3{% endmathjax %}個節點或者第{% mathjax %}7{% endmathjax %}個節點上會合，而{% mathjax %}N=4{% endmathjax %}時，會發現在第{% mathjax %}\lambda-4{% endmathjax %}個節點或者第{% mathjax %}6{% endmathjax %}個節點上會合。那麼最後我們來試著考慮著{% mathjax %}N=M{% endmathjax %}的情況，而{% mathjax %}M{% endmathjax %}的數值範圍為{% mathjax %}[1,∞){% endmathjax %}


![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238047/Algorithm/FloydCycleDetect/NMandCycle_dmotfd.png)

當烏龜進入循環時，兔子的預期位置變成：

{% mathjax %}H'=H+M{% endmathjax %}

在這裡我們還不確定這種情況是否同樣地使烏龜和兔子會在同一點會合，所以我們先假設他們肯定能在某一點會合來驗證其正確性。

我們考慮著以下式子：

{% mathjax %}H+M ≡ T\ (mod\ \lambda){% endmathjax %}

根據前面所述的第ㄧ、二觀察結果，兔子必須至少得繞ㄧ圈才有機會與烏龜會合，但這樣單純繞幾圈也只是與烏龜保持{% mathjax %}M\ (mod\ \lambda)-(\lambda-2M) (mod\ \lambda){% endmathjax %}個節點的差距，所以兔子和烏龜還必須在繞幾圈之後再多>走個幾步才有機會會合，所以烏龜式子會變成如下：

{% mathjax %}T=N_1\lambda+N_2{% endmathjax %} (烏龜繞了{% mathjax %}N_1{% endmathjax %}圈又{% mathjax %}N_2{% endmathjax %}步)


接著將上式代入{% mathjax %}2T + M ≡T\ (mod\ \lambda){% endmathjax %}就會是：

{% mathjax %}2(N_1\lambda+N_2) + M ≡ N_1\lambda+N_2\ (mod\ \lambda) {% endmathjax %}

根據{% mathjax %}mod\ \lambda{% endmathjax %}，我們可以化簡成：

{% mathjax %}2N_2 + M ≡ N_2\ (mod\ \lambda){% endmathjax %}

根據先前{% mathjax %}N=2-4{% endmathjax %}情況得到的觀察結果，會發現都會在第{% mathjax %}\lambda-N{% endmathjax %}個節點會合，那麼同樣地將其結果套用在上式時，

{% mathjax %}N_2=\lambda-M{% endmathjax %}

會發現式子會變成如下:

{% mathjax %}2(\lambda-M) + M ≡ (\lambda-M)\ (mod\ \lambda){% endmathjax %}

再稍微用{% mathjax %}mod\ \lambda{% endmathjax %}來化簡，則會是：

{% mathjax %}-M≡-M\ (mod\ \lambda){% endmathjax %}

而這相當於在第{% mathjax %}\lambda-M{% endmathjax %}個節點或者第{% mathjax %}\lambda-N{% endmathjax %}個節點會合

{% mathjax %}\lambda - M ≡ \lambda-M\ (mod\ \lambda){% endmathjax %}

從這樣推論驗證了{% mathjax %}N{% endmathjax %}在{% mathjax %}[1,∞){% endmathjax %}範圍內的節點這相當於在第{% mathjax %}\lambda-M{% endmathjax %}個節點或者第{% mathjax %}\lambda-N{% endmathjax %}數所構成循環時可以使兔子和烏龜在第{% mathjax %}\lambda-N{% endmathjax %}個節點會合，其中{% mathjax %}N=M{% endmathjax %}。


補充：其中{% mathjax %}\lambda-M{% endmathjax %}中的{% mathjax %}\lambda{% endmathjax %}其實原本是考慮成{% mathjax %}N’\lambda{% endmathjax %}，但由於最後還是會>因爲{% mathjax %}mod\ \lambda{% endmathjax %}而跟{% mathjax %}\lambda-M{% endmathjax %}的最後結果一樣，且如果寫{% mathjax %}N’\lambda-M{% endmathjax %}的話，會不容易理解，在這裏簡化成最後解。



此外，如果讀者願意花更多時間觀察的話，只要畫個圖並標示起點、會合點、距離的話(如>下圖），會發現只要{% mathjax %}N{% endmathjax %}與{% mathjax %}\lambda-N{% endmathjax %}相加就能構成循環長度，換言之，從起點1到會合點之間的節點數剛好是循環的長度。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238046/Algorithm/FloydCycleDetect/2length_s3nzob.png)

另外剩下沒包含到的節點(用橘線來表示的節點)數量剛好會是循環外的節點數{% mathjax %}N{% endmathjax %}。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238047/Algorithm/FloydCycleDetect/3length_zigdvm.png)

還有如果我們限制烏龜只能在循環內走不到一圈來和兔子會合，會得到一個有趣的觀察結果，其中烏龜走不到半圈時會使兔子永遠會合不了，因此烏龜的步數要滿足走不到一圈以及會合的條件必須是半圈以上至一圈的範圍，所以原本的式子會改變成如下：

{% mathjax %}T=\frac{\lambda}{2}+M_1{% endmathjax %}

代入式子{% mathjax %}2T + M ≡ T{% endmathjax %}會形成：

{% mathjax %}2(\frac{\lambda}{2}+M_1) + M ≡ \frac{\lambda}{2}+M_1\ (mod\ \lambda){% endmathjax %}

同時我們可以用先前得到的驗證結果來反證這樣子是否出現矛盾，首先右邊的式子在這前提下，必須等於{% mathjax %}-M{% endmathjax %}或者{% mathjax %}\lambda-M{% endmathjax %}，那麼

{% mathjax %}M_1=\frac{\lambda}{2}-M{% endmathjax %}

將這個假設結果代入式子{% mathjax %}2M_1 + M ≡ \frac{\lambda}{2}+M_1\ (mod\ \lambda){% endmathjax %}就變成：

{% mathjax %}-M≡-M\ (mod\ \lambda){% endmathjax %}

這樣子的結果等同於先前驗證結果，換言之，烏龜只需要繞半圈以上至一圈的距離就能和兔子會合。

補充：先前我們假設烏龜和兔子會花好幾圈又幾個節點才能使他們會合，在這好幾圈又幾個節點的範圍內包含了無數個排列組合，比如2圈又5個節點，現在我們利用限制發現了其實烏龜走不到半圈就能會合。但這過程中，兔子還是得至少走一圈才能會合。

基於這幾個推論結果，我們可以更改{% mathjax %}N=0{% endmathjax %}的觀察結果：

1. 兔子得走完一圈才有辦法跟烏龜會合(p.s 他們倆不動也能會合XD，但這不是在該方法的討論範圍內)。
2. 考慮著循環內外節點數時，兔子{% mathjax %}H{% endmathjax %}和烏龜{% mathjax %}T{% endmathjax %}在循環內的位置關係會是{% mathjax %}H'=2T+N{% endmathjax %}，而{% mathjax %}N{% endmathjax %}是節點外的節點數。
3. 當兔子{% mathjax %}H{% endmathjax %}和烏龜{% mathjax %}T{% endmathjax %}都從循環外部走到循環內部時，我們可以對{% mathjax %}H{% endmathjax %}和烏龜{% mathjax %}T{% endmathjax %}使用同餘{% mathjax %}(mod\ \lambda){% endmathjax %}的概念(如下式)來確定是否存在循環，若兩者的餘數都一樣那就表示存在著循環；反之，就是不存在。(其中{% mathjax %}N{% endmathjax %}為循環外的節點數)

{% mathjax %}H + N ≡ T\ (mod\ \lambda){% endmathjax %}

4. 延伸第三個觀察結果，會發現兔子和烏龜的會合點會是第{% mathjax %}\lambda-N{% endmathjax %}個節點或者第{% mathjax %}\lambda-M{% endmathjax %}個節點。
5. 循環起點到會合點的節點數可以和循環外的節點數構成循環長度，換言之，{% mathjax %}\lambda = {% endmathjax %}循環起點到會合點的距離+ 循環外的節點數。
6. 烏龜只需要繞半圈以上至一圈的距離就能和兔子會合。

第{% mathjax %}1{% endmathjax %}個觀察結果因爲本身不受循環以外的節點數影響，所以不會進行變動，但原本第{% mathjax %}2-3{% endmathjax %}個結果會隨之影響，使之擴展成考慮成{% mathjax %}M{% endmathjax %}個循環以外的節點，而第{% mathjax %}4-5{% endmathjax %}個結果則是因爲第三個結果的推論過程而新增過來的。


## Pseudo Code

根據Floyd's Cycle Detection所描述的演算法而寫出的Pseudo Code，其中使用{% mathjax %}next[i]{% endmathjax %}>和{% mathjax %}head[i]{% endmathjax %}來分別代表變數{% mathjax %}i{% endmathjax %}的下一個節點以及其頭部節點，而NIL在這裡代表不存在任何節點。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238048/Algorithm/FloydCycleDetect/PseudoCode_hascycle_asngap.png)

該演算法以List為輸入參數，當List輸入進去時，會先設定其頭部的位置給兔子和烏龜這兩個變數，接著為了他們兩個變數能夠在不影響系統的情況下跑遍整個List結構，所以設定了While以及其條件{% mathjax %}Hare\ \neq\ NIL{% endmathjax %} and {% mathjax %}next[Hare] \neq NIL{% endmathjax %}，其條件主要會檢測目前兔子所走的位置是否能繼續走，最後兔子和烏龜會依照規則來走指定步數，當他們所在的位置是一樣時，就代表著此List結構確實存在循環而回傳True，反之兔子走到盡頭都沒遇到烏龜而回傳代表不存在循環的False。


## Implementation

程式碼連結：[bit.ly/2FKotVP](http://bit.ly/2FKotVP)

使用EAFP程式碼風格來取代過度的if-else檢查，並從中提升速度，另外先讓在try區塊中的兔子多走一步以避免while迴圈判斷到錯誤的情況，同時這樣子的移動方式並不會改變兔子和烏龜的會合結果，只不過變成{% mathjax %}M+1{% endmathjax %}個循環節>點的情況來移動。

## Performance

時間複雜度：考慮該方法應用在不存在循環以及存在循環的場景中，時間複雜度範圍會是{% mathjax %}O(N_1+N_2){% endmathjax %}-{% mathjax %}O(\lambda+N){% endmathjax %}，其中的{% mathjax %}N_1{% endmathjax %}是循環外的起點1至循環內的起點2的節點數，而{% mathjax %}N_2{% endmathjax %}是循環內的起點2至烏龜與兔子預計會合點之間的節點數(如下圖)，而{% mathjax %}\lambda{% endmathjax %}是循環的長度以及N為循環外的節點數，{% mathjax %}N_1{% endmathjax %}、{% mathjax %}N_2{% endmathjax %}、{% mathjax %}\lambda{% endmathjax %}和{% mathjax %}N{% endmathjax %}這四個大小關係會因>爲第六個觀察結果而會是{% mathjax %}\lambda+N≥N_1+N_2{% endmathjax %}。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1627238047/Algorithm/FloydCycleDetect/N1N2length_iaxbil.png)

空間複雜度：該方法本身不需要向系統索求額外記憶體空間或者內存來進行判斷，所以空間複雜度會是原本一般執行程式碼所需要用到的記憶體大小，也就是{% mathjax %}O(1){% endmathjax %}。


## Conclusion
我們利用一些場景來說明循環問題，接著將這些場景轉化成電腦可以判斷是否有循環的List結構，最後我們提出知名的龜兔賽跑算法來幫助電腦判斷該List結構是否有循環。除此之外，我們也額外提供讀者一些章節來描述該方法是如何成功地判斷、如何實現以及其算法的執行成本。
