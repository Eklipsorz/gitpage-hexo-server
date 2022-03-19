---
title: Event loop 與 在JavaScript中的它
date: 2021-11-14 15:29:12
tags:
 - JavaScript
---

## JavaScript 背景
由網景和微軟掀起瀏覽器大戰中，瀏覽器的網頁在早期只能透過HTML檔案內容將一些文字和內容顯示在瀏覽器上，而這樣的內容就只是在電腦上顯示一張大海報，網景為了近一步打敗微軟而開發出能與網頁互動的程式語言-JavaScript，讓瀏覽器去執行網頁上的JavaScript來讓使用者的互動藉由該語言來實現特定效果，但後來微軟憑藉著作將瀏覽器綁定自家的作業系統來壟斷整個瀏覽器市場，最後打贏了網景，但網景所衍生出的JavaScript並未就此消失，而是進一步以Open Source的形式存活著並且成長為一種很成熟的語言。

## JavaScript 的執行緒
JavaScript的出生是因應想要讓使用者對網頁內容進行某些互動來執行某些任務，甚至可以透過改變DOM來進一步改變網頁，其中透過DOM來改變網頁是它的最大特點，但DOM的節點本身在多個執行緒下的執行環境是一個很容易被改變且會發生預期外的結果或者使網頁無法正常，比如說有多個執行緒，每一個執行緒都想對同一個DOM節點做寫入、刪除、讀取的動作，若讓負責刪除的執行緒先執行，那麼剩餘做讀取、寫入的執行緒會因為找不到該節點而出錯。

為了避免這樣子的問題發生，JavaScript 本身就被設定成只會有一個執行緒來執行任務，而在瀏覽器執行它的時候，其負責解析語言的元件只會建立執行一個主執行緒來負責解析並執行JavaScript本身，並從這個主執行緒來生成單一的執行緒來給予CPU執行，同一個時間點只會有一個任務被執行。

當然這只是建立在JavaScript本身的限制，實際上來說執行環境(如瀏覽器、Node.js)會因應HTML5所提出的Web Worker標準而開放額外的API來讓JavaScript去調用並在執行環境上建立除了主執行緒以外的執行緒，而這些執行緒會受到主執行緒來控制，並且不能夠操作DOM節點，這樣子的提供不僅增加效能，也保證DOM節點會是單個執行緒(也就是主執行緒)來執行。

* JavaScript之所以為直譯語言，是原本就為了盡可能讓開發者快速進行網頁上開發好拓展網景瀏覽器的市場，同時也盡可能減少編譯時所帶來的額外成本，但缺點就是語言會是弱型別且每一次執行都要重新解析並執行。

* Main Thread 是瀏覽器底下的Renderer Process底下的Thread，而Renderer Process主要是渲染引擎的執行實體，負責解析同一個網頁下的HTML、CSS並計算每個網頁元素的位置以及實際上的繪製、執行JavaScript程式碼，而Main Thread是主要是負責包攬這些工程的thread，主要是解析同一個網頁下的HTML、CSS並計算每個網頁元素的位置並生成對應的繪製指令給其他同為Process下的Thread進行繪製、執行JavaScript程式碼，而Main Thread的數量本身會是單個執行緒。

* Web Worker 標準是為了盡可能減緩單執行緒所引發的blocking問題 - 前面的任務會因為等待或者執行關係而拖延到後面的任務，進而讓後面任務無法正常執行，解法就是允許JavaScript去額外產生Worker Thread去分擔這些任務，好讓Main Thread順利執行，同時間只允許Main Thread操作DOM節點，其他Worker Thread不得執行，這是為了滿足JavaScript的設計初衷 - 同時間不得有多個執行緒去對DOM節點做處理，甚至是同一個節點的處理。


## 電腦處理任務的方式

一般來說，一個程式碼若要確實被執行，過去一定得將程式碼封裝成Process這基本形式來讓Scheduler去分配實體CPU執行，另外在這裏會用任務來稱呼特定功能的程式碼，並非真的是指能被當排程的基本單位，當封裝成Process形式之後，就會被放入一個準備佇列(Ready Queue)等待Scheduler排到他，最前面的任務會被Scheduler按照任務性質來將任務分配至特定的佇列(Queue)，每一個佇列都對應著實體CPU，等待Scheduler分配完最前面的任務後，再由次先的任務進行分配，後面以此類推，當然對應CPU的佇列也都是如此，會先從自己對應的佇列中挑第一個任務執行，然後執行完隨後挑次先，一直到佇列為空。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636632652/blog/event/eventloop/scheduling_szgjwp.png)

而現代作業系統不再以Process形式來當基本排程形式，而是以執行緒(Thread)為基本排程形式來給Scheduler，具體實現方式是將一個程序X上的功能切分成分成好幾份子功能並按照份數來建立執行緒，每個執行緒會從中拿到獨立功能來執行並且共享(可能會共享著程序X的變數、函式)著同一個程序X來代表著程序X執行某些功能，而這樣子的切分會由於同個程序X上的Thread為單位來佔用多個CPU資源執行，進而使一個程序X所發揮出來的效率會比單純以Process來排程來得好，畢竟後者最多只能拿到一個CPU資源來執行。在這裡我們皆用任務來稱呼每個要被排成的執行緒。


而電腦處理任務的方式，同步處理和非同步處理，理論上，同步處理下的任務們只會在同一個時間點內執行一個任務，而且每一個任務皆必須等待上一個的任務結束才能執行，而非同步處理下的任務們可以各自被執行，而且每一個任務都不用等待任何一個任務就能繼續執行。


然而，實際上會按照實體CPU的數量以及是否支援非同步處理，若CPU數量只有一個的話，只能夠支援同步處理，也就是一次只能執行一個任務(如下圖)。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636720649/blog/event/eventloop/singleCPU_xvyctj.png)

但若CPU數量多於一個的話，就能支援非同步處理，在這情況下就同時執行執行多個任務，但不能夠說完全不用等待，因為實體CPU也就N個，若要排進CPU的任務大於N個時，勢必會有任務需要等待前面的任務完成才能做。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636720649/blog/event/eventloop/multipleCPU_w9o1hc.png)

## JavaScript 如何處理任務
首先電腦本身只能看懂由0和1所構成的機械碼，它不會明白JavaScript的語法在講些什麼，而當時設計者為了讓開發者能夠快速開發來強占瀏覽器的市場而捨棄較嚴謹的編譯語言，改由直譯語言，其中編譯語言是一種事先將程式的語法(偏易於人類看懂的語法)轉譯成機械能看懂的形式，並直接讓執行環境去執行的，但由於可以事先轉譯，基本會要求開發者寫出的程式碼語法要先滿足一定程度的規則才能成功轉譯成機械能看懂的形式，比如要求開發者告知每個資料和變數的資料型別是什麼。而直譯語言則是不用事先編譯成機械碼，而是透過一個程式X來一行一行邊讀取程式碼邊轉譯成機械碼來執行，而這個程式X會是解釋器，通常執行成本會比編譯語言來得高，但透過不煩瑣的規則來讓開發者能夠快速開發。

實際上來說，JavaScript本身就實現著同步處理，瀏覽器會提供一個Main Thread來負責解析並以JavaScript解釋器(常以一個專門解析的JavaScript引擎來代表)來建立一個執行緒給予CPU的Scheduler來分配實際CPU去執行，進而滿足JavaScript的直譯和單一執行緒這兩個先決條件，而該Main Thread會是源自於主要負責同一個網頁的渲染和事件處理的Renderer Process，如將網頁解析成Render Tree、處理Layout、執行Paint等等

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641137948/blog/event/eventloop/simpleBrowserSystem_l1y2px.png)

在這裏由於瀏覽器的Main Thread只會生成一個執行緒去執行，而JavaScript本身是依照一行一行來讀取並轉譯執行，也就是說同一個時間內只會有一個任務(夾帶著轉譯後的JavaScript)能夠被Main Thread執行，而後面的任務(夾帶著轉譯後的另一個JavaScript)則必須等前面的任務被CPU執行完成才能被執行，從這樣子來看，就是個典型的同步處理，而在這裡由Main Thread所生成出來的單個執行緒儼然對他們而言就是個實體CPU。

### 同步處理的常見問題 - Blocking
根據同步處理的特性：
>同步處理下的任務們只會在同一個時間點內執行一個任務，而且每一個任務皆必須等待上一個的任務結束才能執行

當目前執行的任務一直使用著CPU資源或者佔用著CPU並向外部請求時，會阻塞(block)後續的任務(程式碼)沒辦法及時執行，而這個現象叫做阻塞(Blocking)，以下用程式碼來示範，下面分別會從foo.com、bar.com、qux.com取得資料，並列印出來，過程中會因為getSync佔用著CPU資源去索求外部資源，並等待外部發送回應給getSync，其結果就是整個程式的執行會因為前面三段getSync而產生阻塞現象。


```
let foo = $.getSync('//foo.com')
let bar = $.getSync('//bar.com')
let qux = $.getSync('//qux.com')

console.log(foo)
console.log(bar)
console.log(qux)
```
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636733654/blog/event/eventloop/BlockingExample_z4gh1m.gif)

## Call Stack
當執行JavaScript檔案時，系統會根據Scope規則來建立較為抽象的執行環境(Execution Context)來概括並辨別該環境能有的變數、函式、狀態等資訊，而整份檔案會是個一個全域執行環境(Global Execution Context)，若遇到函式或者由括號建立的執行環境時，會是在全域執行環境下建立一個區域性執行環境(Local Execution Context)或者函式執行環境(Function Execution Context)，所有在這個區域性執行環境的變數和函式都只會在這個環境下存活並能夠被辨別以及執行。

在這裡為了要清楚辨別全域執行環境和哪些區域執行環境先被執行，每當一執行JavaScript檔案，就先建立全域執行環境並推入(Push)Stack中，接著當這環境下有段會呼叫函式X時，會建立以函式X為主的區域性執行環境X來記錄，並且推入至Stack的最上面，而當在目前區域環境X中遇到了return語法 或者 沒東西可讓環境X繼續執行(存在)，就表示該環境X要回傳並釋放記憶體，此時就會從Stack移出(Pop)最上面的環境X(也就是目前執行環境X)。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641141910/blog/event/eventloop/CallStackDiagram_znytvk.png)


而當該全域環境執行完畢時，便也會從Stack移出它。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641142253/blog/event/eventloop/popGlobalExecutionContext_vzsf1o.png)

* 在這裡，若要先執行JavaScript程式碼的話，都會先被系統放入JavaScript引擎所給予的call stack環境來給予引擎解析JS、執行、呼叫額外API。

### Call Stack 例子
在這裏我們建立三個函式分別為funct1()、funct2()、funct3()，然後在定義他們之後呼叫他們去執行來觀察他們與Call Stack之間發生什麼事情，範例程式碼如下：
```
function funct1() {
    console.log('funct1')
    funct2()
}

function funct2() {
    console.log('funct2')
    funct3()
}

function funct3() {
    console.log('funct3')
}

funct1()
console.log('end')
```

在這裏是採用Loupe平台來觀測程式碼和Call Stack之間的互動，在這個平台中並不會把main function特意顯示，程式碼結果如下，首先系統會先呼叫funct1()，執行funct1後便將funct1()推入在Call Stack中去紀錄，然後去印funct1這字串，接著funct1()又呼叫funct2()，這時會將funct2()推入在Call Stack中紀錄，funct2()會在funct1()之上，然後去印funct2這字串，最後再由funct2()呼叫funct3()，這時會將funct3()推入在Call Stack中紀錄，funct3()會在funct2()之上，然後去印funct3這字串。


![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636728641/blog/event/eventloop/CallStackExample_kay9ld.gif)

而funct3執行完畢後，也沒呼叫什麼以及執行什麼，此時Call Stack會移出目前最上面的函式-funct3()，而系統會在移出後自動抓最上面的函式-funct2()來返回funct2()呼叫funct3()的地方，接著funct2()也沒呼叫什麼以及執行什麼，此時Call Stack會移出目前最上面的函式-funct2()，而系統會在移出後自動抓最上面的函式funct1()來返回funct1()呼叫funct2()的地方，接著funct1()也沒呼叫什麼以及執行什麼，就便讓Call Stack移除目前最上面的函式-funct1()，最後回到main function呼叫funct1()的地方，隨後在印個end這字串

### Call Stack 極端例子 - 不斷地呼叫
當函式一直不斷地呼叫時，會不斷往Call Stack推入更多函式，這會使得Call Stack本身被佔用，以下面的程式碼做為例子，程式碼會先定義funct1()，其內部會是呼叫funct1()，接著在呼叫funct1()，最後結果會是讓funct1()不斷被自己呼叫，直到被開發者中斷、系統自己中斷才停止。
```
function funct1() {
    funct1()
}

funct1()
```

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636732264/blog/event/eventloop/infExample_boi9hh.gif)

## 什麼是事件

當對特殊元件做出某些行為時，這些元件會發射對應行為的信號，然後再藉由攔截信號來執行的特定任務X，在這裏信號正是被稱作為 "事件(event)"，而特定任務X則是這個事件發生時所要做的事情，事件和特定任務X之間的互動可以實現使用者對於軟體上的元件互動，比如點擊某個按鈕就跑出一系列的清單、滑動卷軸能夠往下看後續內容。而為了實現整體事件系統的處理，勢必會得要有一個機制在軟體Y上來監聽某種信號/事件以及產生對應的特定任務X。

### 如何實現事件？

在這裏的軟體Y和被監聽的元件之間會有二種方式來實現：
1. pull (polling)：由軟體Y在固定時間內產生任務去詢問對應元件是否要產生事件/信號，若詢問結果是要產生，便直接執行對應的特定任務X，但詢問一直失敗很容易造成不必要的效能損益，尤其是關心的元件可能不止於1種，可能總加起來的數量會是N個，那麼負責詢問的任務數量在固定時間內也是N個。
2. push：由發送信號的元件來通知軟體Y處理，在事件發生前，軟體Y可以做自己的事情，在這裡軟體Y可以事先告知元件當事件發生時該如何處理，或者一律選擇由軟體Y來處理事件。


一般來說，軟體Y為了效率而可能會採用push這個異步處理來實現事件，甚至在當元件上的特定事件發生時，就會產生特定的執行緒來執行每個事件的特定任務，雖然效率上，可以藉由這樣而不把事件處理全給軟體Y的主要執行緒上，但實際上來說，若這些執行緒彼此間共用相同資源時，會產生一種名為Critical-Section Problem 或者 Race Condition，且由於事件對應的特定任務會因為事件的不可預知性而使這個問題更難解決，另外事件就算給予軟體Y主執行緒來執行，還是因爲共享資料的關係而不能保證這些執行緒不會有這些問題。

* push：原意為在不通知對方的情況下，先透過網路發送資訊至對方，在這裡是指特殊元件發送通知資訊至軟體或者某個特殊程式模組
> to send information over the internet without receiving a request for it first

* pull：透過主動的詢問或者搜尋來從網路上獲取資訊，在這裡是指軟體或者某個程式模組去從發送信號的元件詢問他是否發生事件或者發射信號
> to get information from the internet, after asking or searching for it

## 事件會發生什麼問題

不論是否為Critical-Section Problem 或者 Race Condition，執行緒本身是共享著同一份程序(Process)的資源並從中分出一個子功能來執行，所以共享資源是難以避免，而每一個要使用共用資源的執行緒肯定存在著存取共用資源的程式碼區塊，而這些區塊被稱之為Critical-Section，當有多個執行緒在這個區塊是存取(Access)同一個資源時，而存取可以是修改、刪除、讀取、寫入等一系列操作，這些執行緒很有可能因為彼此都想要存取同一個資源而使每個執行緒的最後執行結果變得不如預期，有些可能因為讀取到其他執行緒修改的值、也有些則是因為讀取到的資源被刪除了，而這種彼此競相存取/修改同一個資源的行為就叫做競爭情況(Race Condition)，此時發生該情況的Critical Section就稱之為Critical-Section Problem。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636791025/blog/event/eventloop/raceConditionDiag_vp78kk.png)

* Critical section：程式碼中負責存取與其他任務共享的資源之程式碼區塊，而section正是指區塊
* Race condition：源自於電路上兩個信號源彼此競爭誰出佔用唯一的輸出管道來進行輸出，在電腦科學裡，是指著多個執行緒彼此競爭誰先存取到同個共享資源。

### 問題例子
假設說有一個任務1想要存取某個資源Y下的X值來處理自己的任務內容，而資源Y本身對於系統而言是可以共享存取的，所以任務1的Critical Section會是存取資源Y的X值之程式碼(如下)，首先會透過id來取得資源Y這個物件，然後透過其資源Y的getXValue來取得X值，預期這個任務1會拿到X=1來做處理，

```
// Critical Section
resource = getResourceById(id)
result = resource.getXValue()
// Critical Section
```

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636642604/blog/event/eventloop/criticalSectionExample1_zflvd8.png)

然而突如其來的特定任務X想要修改該資源Y下的X值，使值從1轉換為2，任務2的Critical Section會是如下，會透過id來取得資源Y這個物件，然後透過其資源Y的modifyXValue(2)來將X值修改成2，
```
// Critical Section
resource = getResourceById(id)
result = resource.modifyXValue(2)
// Critical Section
```

若這兩個任務同時都各由獨立的CPU資源來執行，執行先後順序會無法從中定義，但若是先從特定任務X執行或者在執行任務1的存取X值的同時下任務X就已經修改的話，任務1只會讀取X=2，而非是讓任務1讀取X=1，這樣子的存取很有可能會讓任務1的執行結果是不如預期，甚至無法正常執行
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636642604/blog/event/eventloop/criticalSectionExample2_jnecq7.png)

## 系統如何解決問題
面對這樣子的Critical-Section Problem，系統主要有兩種方式，第一種使用鎖(lock)機制，讓先存取資源的人能夠鎖住資源，不讓其他人在同時間使用資源，第二種使用事件迴圈(event loop)，這種會強制將要存取Critical Section的任務轉換為同步處理，透過同步處理的特性-同一時間只允許一個任務執行，不過這兩種只是阻止同時間的修改，若先修改同一份資源的任務先執行的話，還是會讓後面執行的任務會出現不如預期的問題，拿先前的問題例子中的任務1和任務X，任務1只要先存取資源Y，就能皆能透過這上述任意方式來保證任務1再存取的同時間不會有任務X來存取或者修改，但是如果任務X先於任務1的話，那麼任務1到最後還是會取到X=2，為此，就有人提出另一種event loop來解決。


### lock 
由於資源本身的存取是透過Critical Section來達成，因此可以被視作為資源的代言人，而 lock 這一個機制會允許任何一個先存取到Critical Section的程式替這個Critical Section或者對應的資源上鎖，使得後面想要存取同一個資源的任務都會被鎖給擋住(Blocked)，而當上鎖該資源並存取資源的任務只要存取完畢，便會把鎖解開讓後面的任務去存取，當然先搶到的任務可以擁有先鎖住資源的權利。除了上述之外，鎖本身會阻塞(Block)想要存取同一個資源的任務，因此若想要存取同一個資源的任務一多的時候，效能很有可能會因為阻塞而衰減。


以問題例子上的任務1和任務X為例，具體實現會是宣告兩個任務都能辨識的lock變數以及基於lock的while來實現lock，將這些分別放置在兩個任務對於同份資源的Critical Section中(內容如下)，而一開始的lock值會是false，當其中一方改變了lock值，另一方能夠看到變動。

```
// 任務1的Critical Section
while (lock) {}
  lock = true
  resource = getResourceById(id)
  result = resource.getXValue()
  lock = false

// 任務X的Critical Section
while (lock) {}
  lock = true
  resource = getResourceById(id)
  result = resource.modifyXValue(2)
  lock = false
```

所以在這裏只要讓任務1先去執行下面程式碼就能透過lock設定為true來鎖住資源Y，而當任務X要去執行的時候會因為lock為true而一直跑無限迴圈，直到任務1存取完資源Y時，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636795235/blog/event/eventloop/lockExample_y0f60k.png)

，直到任務1存取完資源時，就便將lock設定為false，使任務X能夠從無限迴圈跳開去修改資源Y的X值為2

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636795564/blog/event/eventloop/lockExample2_nt7vjj.png)


### simple event loop
event loop 是指著負責分發適當任務執行的流程迴圈，也就是會重複做相同分發任務執行，只是任務會隨著分發而有所不同，本身會運用一個佇列(Queue)、從佇列中挑出一個最前面的元素、挑出的元素只給主執行緒來執行這三個因素來將原本設定為異步處理的任務轉換成以同步處理的任務，從而解決Critical Section Problem 以及 鎖帶來的阻塞問題，具體方式如下，每一次由事件觸發而衍生出事件處理任務皆放入任務佇列(Task Queue)，而負責轉交給Scheduler或者執行任務的執行緒只會有一個，當中會從佇列的最前面任務挑出來放入至主執行緒去做執行，接著分派給Scheduler去執行，當執行完之後便會挑選下一個在佇列的任務，直到清空。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636798774/blog/event/eventloop/simpleEventLoop_jdc87i.png)

所有放在佇列中的任務全都會如同步處理那樣：
>同步處理下的任務們只會在同一個時間點內執行一個任務，而且每一個任務皆必須等待上一個的任務結束才能執行

而這樣子的處理也避免使用鎖以及鎖帶來的阻塞現象，當然也得要考量每一個被執行的任務是否本身就是個阻塞其他任務的任務。這方法本身效能會因受限於只有單執行緒能執行以及像鎖那樣無法完全避免Critical Section Problem帶來的不可預期之執行結果。

* 這裡提到的主執行緒或者main thread並不是指Renderer Process下的Main Thread

### complex event loop
為了進一步提升效能和緩解Critical Section Problem帶來的不可預期之執行結果，就額外添加多執行緒以及額外Queue來延遲真正造成不可預期的任務，在這裏由一個主要執行緒根據哪些任務是真正容易對共用資源造成不可預期的任務來分發至不同地方來執行：擁有多個執行緒的空間(Thread Pool)、暫緩執行用的空間，通常會是對某些內容進行寫入、刪除、變更的功能會被歸納成容易對共用資源造成不可預期的任務，讀取、修改某副本內容、沒涉及共用資源的功能則是歸納成較安全的任務(Thread-safe)。


首先一開始還是會由每一次由事件觸發而衍生出事件處理任務都放入任務佇列，由主要執行緒(Main Thread)從佇列中挑選第一個任務並判別其性質進行分發，若是不會對共用資源造成任何修改的任務，則會被放入Thread Pool，在這裏會分配多個執行緒去執行任務，而若主要執行緒判別任務為會對共用資源造成任何修改的任務，則放入另一個Queue等待適當時機回到Main Thread執行

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636803337/blog/event/eventloop/complexEventLoop_ocm5jl.png)



Thread Pool本身是由固定數量的執行緒組成，平時會保持著可幫助任務執行的狀態，但由於數量有限，所以會用佇列來存放被放進Thread Pool的任務，等著哪些執行緒是可幫忙執行的狀態再來從佇列中挑選前面的任務來執行，當沒有執行緒可用時，便會停止挑選直到有可用的執行緒。所有被執行緒執行完的任務會回傳至主執行緒做最後的處理以及回傳結果。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636804100/blog/event/eventloop/threadPoolOnEventLoop_rmfugm.png)

而被真正容易對共用資源造成不可預期的任務會被放進一個Queue，當然這個佇列也是等待著執行緒去執行，只是負責執行的執行緒和執行方式都跟上述有所不同，由於這些任務很容易對共用資源造成不必要且不可預期的結果，所以會試著等其他要使用共用資源的任務執行完再來從這個佇列挑出來至主執行緒來執行。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636804544/blog/event/eventloop/queueOnEventLoop_ilrszi.png)

整體來說，這個event loop架構會盡可能透過開放多執行緒讓任務們的執行效率提高以及藉由佇列來分離出一些很容易對共用資源造成不必要且不可預期的任務並放進另一個佇列等待時機在主執行緒執行。

* 這裡提到的主執行緒或者main thread並不是指Renderer Process下的Main Thread

## JavaScript 的事件

在這裏的JavaScript由於本身時常應用於瀏覽器，所以會以瀏覽器的事件來進行說明，首先當瀏覽器將網頁上所有元件轉換成適當的實體模型以及計算每個元件的實體位置、大小等資訊時，便賦予網頁上的所有元件在執行環境(瀏覽器)上是具有事件接收和事件觸發的物件，而所有對事件/信號的發送會由瀏覽器(瀏覽器還調用著作業系統的API來接收信號)負責轉送。 

也就是說當使用者對著元件X進行點擊且設定名為onElementXClicked函式來當作事件發生所要做的處理時，addEventListener本身會先建立專屬於元件X的EventListener物件，該物件會以清單的形式來紀錄元件X上所有綁定的事件種類和對應事件處理內容，而瀏覽器實際上會在利用先前提到的資訊計算、實體模型去判斷這個點擊信號是屬於哪個DOM物件，然後鎖定該DOM物件就檢測其物件是否有相關事件物件(比如EventListener物件)，若有的話，就根據事件種類來找尋對應的函式來當作事件處理，在這裏函式會是onElementXClicked，所以點擊元件後會顯示hi這字串
```
elementX.addEventListener('click', function onElementXClicked(event) {
  console.log('hi)
})
```

### Event Flow 
然而，如果使用者對著巢狀結構下的子元件來進行互動時，比如類似於程式碼中的元素3(element3)，那麼瀏覽器該如何判定這次的互動/事件是屬於哪個元素呢？瀏覽器大可直接根據事件是源自於哪裡來將事件歸類於element3，並且由它的事件處理來處理
```
<element1>
   <element2>
   	<element3>
		content
	  </element3>
   </element2>
</element1>

```

可由於是巢狀關係(如下圖)，元素3(element3)被元素2(element2)所包含著，而元素2(element2)被元素1(element1)包含著，嚴格來說，當使用者對元素3(element3)做互動，同樣地也對著另外兩個元素進行互動，更甚至說包含這些元素的元素(比如body)也跟著互動。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1630587482/blog/event/threeElements_lohr6c.png)


這時，瀏覽器可以有幾種選擇去決定事件是屬於哪個元件，第一種選擇是按照之前的規則，直接將事件歸類於元素3(element3)並發送信號給元素3，並對元素3的EventListener來找尋對應的事件處理，另外一種則是使用事件流(event flow)將訊號傳遞給每個包含元素3的元素，並試著對這些元素的EventListener找尋對應的事件處理，只是傳遞方向又可以細分兩種，一種是從最外圍的元素1開始往內傳遞的事件捕獲(event capture)，也就是信號會先傳遞至元素1，接著在傳遞元素2，最後傳遞元素3，另外一種則是從元素3往外傳遞信號的事件冒泡(Event bubbling)，也就是信號會先傳遞至元素3(發生事件的來源處)，接著在傳遞至元素2，最後傳遞元素1。

不過實際上來說，瀏覽器傳遞信號會一次分三種階段來傳遞信號，當對一個目標元件X產生互動時，第一個階段會先從包含元件X的最外面元件到內部元件來傳遞信號的捕獲階段(Capturing Phase)，第二個階段為從目標元件X的父元件傳遞信號至目標元件X的目標階段(Target Phase)，最後一個階段會先從目標元件X往外傳遞信號的冒泡階段(Bubbling Phase)，當使用者對某個元件X進行互動時，瀏覽器會先透過第一個階段來傳遞信號，接著就是第二個階段：將信號傳遞至實際發生事件/互動的元件X，最後就是第三個階段傳遞信號。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636879992/blog/event/currentPropagationPath_rj9x5j.png)


## JavaScript 上的 Event Loop
當瀏覽器按照event flow傳遞事件/信號的時候，若單純由JavaScript負責接收事件/信號與事件處理的話，會因爲在(瀏覽器)Renderer Process中的主執行緒只會產生一個執行緒執行而容易發生阻塞現象，讓後續的任務無法繼續做，因此瀏覽器或者執行環境為了補足這塊而提供Web API讓JavaScript能夠額外建立worker thread去處理接收事件/信號與事件處理，隨後為了要讓主執行緒處理後續的處理結果，會將這些結果以及主執行緒事先給定好的處理方式封裝成任務至對應的任務佇列，主要的任務佇列有以下幾種，主要會按照使用的語法、是否較快執行、佔比是否較小、是否為渲染來分配。
  - Mircotask Queue：如同其名，主要存放執行較快且資源佔比較小的任務，如Promise，主要會從Web API那邊接收任務
  - Render Queue：如同其名，主要存放負責畫面渲染的任務，如計算每個網頁元素的佈置方式、大小等等，主要會從Web API那邊接收任務或者由其他任務或者自身負責的渲染業務。
  - Marcotask Queue：如同其名，對比於Mircotask，主要會存放執行較慢且資源佔比較重的任務，如event、DOM，主要會從Web API那邊接收任務

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641308751/blog/event/eventloop/baseBrowserModel_valbif.png)

除了這三個佇列以外，Call Stack會是JavaScript引擎所提供的堆疊，主要放著目前主執行緒在執行的JavaScript程式碼，當Call Stack為空時，就會透過event loop的機制-從佇列中取出任務分配至適合的地方來執行，接著睡眠等待下一個取出任務的週期發生，但在這裡會有三個佇列，考量到Marcotask和Mircotask本身會影響Render Task所處理的畫面以及他們的執行快慢，所以為了 **盡可能讓Render Task能夠先以最新畫面來渲染，會先讓最快執行的任務-Mircotask優先執行，接著Render Task在隨後執行，最後執行較慢的Marcotask 就放到後面** ，順序會是：
  - 先挑Mircotask Queue的最前面任務並移出至Call Stack來給JavaScript引擎執行，當Mircotask Queue清空就挑下一個Queue
  - 挑Render Queue的最前面任務並移出和轉換成對應的繪製指令給同種Process下的其他負責GUI渲染的執行緒來負責實際畫面渲染，當Render Queue清空就挑下一個Queue
  - 挑Marcotask Queue的最前面任務並移出至Call Stack來給JavaScript引擎執行。

* 除了Mircotask Queue、Render Queue、Marcotask Queue以外的佇列，其實還有比Mircotask更快更輕量的任務，該任務會放在名為nextTick Queue，在該佇列中的任務被稱之為tick task(tick 原文意旨時間極短的)，但在這裡為了展現主要會有的任務種類而只提出三種佇列。

* 關於佇列被event loop挑宣的優先權會是：nextTick Queue 為第一，Mircotask Queue為第二，Render Queue為第三，Marcotask Queue為第四。

* Micro vs. Marco ：前者形容輕量且小，後者形容較重且大，形容任務的話，Microtask 是指執行較快且資源佔比較小的任務，Marcotask 是指執行較慢且資源佔比較大的任務

* JavaScript 程式碼主要區分為tick task、microtask、marcotask，不會因為事先放在call stack或者先執行就改變他們所具有的任務種類，比如原本為marcotask 的console.log先放在call stack執行，而不是以callback形式來執行，該任務還是因爲本身執行較慢且相較於其他任務，資源佔比較重，所以還是仍為marcotask

* Macrotask 總是在 JS 代碼執行完成並且 Microtask Queue 清空之後執行


### Event Loop 例子

假設負責讀取內容的執行緒讀取到一個夾雜JavaScript的HTML檔案，若其JavaScript程式碼先於其他HTML元素出現，這時主執行緒就會將這些JavaScript程式碼包裝成任務或者環境在call stack執行，此時負責讀取內容的執行緒仍繼續讀取JavaScript之後的HTML程式碼，當它便把這些程式碼也包裝成任務放進主執行緒內部的Render Queue，這時call stack和Queue會是如下狀況：
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641311954/blog/event/eventloop/EventLoopSimpleExampleStep1_gpndcm.png)

當call stack一清空時，主執行緒就會按照佇列優先權來挑一個佇列清空，這時只有Render Queue是還有任務的，所以主執行緒就會透過event來挑該佇列的第一個任務並移出轉換成對應的繪製指令給其他負責實際渲染的執行緒去做渲染，第一個被移出之後，隨後再挑第二個渲染任務，一直到佇列為空：

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641311955/blog/event/eventloop/EventLoopSimpleExampleStep2_uxukin.png)

接著之後，若負責讀取內容的JavaScript檔案中的程式碼包含以下程式碼，這段程式碼會間接呼叫WebAPI中的DOM來將onElementXClicked當作是特定元件X的點擊事件所要做的處理，並建立額外Thread去負責監聽該特定元件的點擊事件和儲存對應的處理-onElementXClicked
```
elementX.addEventListener('click', function onElementXClicked() {
  // do something
})
```

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641312606/blog/event/eventloop/EventLoopSimpleExampleStep3_zcxjfz.png)


當特定元件X被點擊一次的時候，由於DOM的事件處理皆為Marcotask，所以負責監聽的Thread接收信號時就按照任務種類將對應的處理放在Marcotask Queue。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641312606/blog/event/eventloop/EventLoopSimpleExampleStep4_ogag3a.png)

此時由於call stack仍為空的且只有Marcotask Queue，主執行緒就透過event loop來將放在Marcotask Queue的第一個任務移出至call stack來執行。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641312606/blog/event/eventloop/EventLoopSimpleExampleStep5_c11qvu.png)


![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1641312977/blog/event/eventloop/EventLoopSimpleExampleStep6_lnsoiw.png)
### 事件下的Event Loop 例子

在這裏以HTML形式設定兩個按鈕元件，它們的id分別為testbtn1、testbtn2，而JavaScript則是為這兩個按鈕添加點擊事件，而對應的事件處理皆為onClick，接著我輪流點testbtn1、testbtn2，一直到點擊第五次，也就是點擊完testbtn1就停下了。

```
// inside js 

$.on('#testbtn1', 'click', function onClick() {
    console.log('button1 is clicked')
});
$.on('#testbtn2', 'click', function onClick() {
    console.log('button2 is clicked')
});

// inside html 

<button id="testbtn1">button1</button>
<button id="testbtn2">button2</button>
```

影片可以點擊下方來看，首先瀏覽器上的JavaScript遇到事件綁定時會呼叫瀏覽器下的Web API去替那個兩個按鈕增加點擊事件，接著當點擊testbtn1時，瀏覽器負責管理事件接收和處理的執行緒會將testbtn1點擊事件時對應的函式-onClick放入至Callback Queue，接著再點擊testbtn2時，瀏覽器會把testbtn2點擊時對應的函式-onClick放入至Callback Queue，然後隨著後續的點擊來把對應的函式放入至Queue，過程中，負責管理事件接收和處理的執行緒會把Queue的第一個函式放入至Call Stack來執行，隨後執行完就被移出，再挑下一個在Queue的函式，直到Queue被清空。
[點擊我看例子](https://youtu.be/50zxZ7GawmA)


### setTimeout
setTimeout 本身是瀏覽器的WebAPI所提供的定時器，當瀏覽器在JavaScript檔案讀取到以下格式時，便會呼叫WebAPI下的setTimeout來執行，此時瀏覽器會建立一個thread去按照secs秒數來等待，等到時間到的時候會把指定函式-funct 放入至Marcotask Queue或者CallBack Queue，而指定函式會跟其他上述提到的事件處理函式一樣，等到Call Stack為空才會將Queue的funct移到Main Thread來執行，而Call Stack會放上funct。 
```
setTimeout(function funct() {
    // do something
}, secs)
```

* 在這裡的CallBack Queue會是Marcotask Queue

### setTimeout 例子1
設定一個等待1秒才執行的計時器，首先當瀏覽器的JavaScript引擎讀取到setTimeout和1000時，就便呼叫瀏覽器的WebAPI-setTimeout來建立一個thread來實現計時器，一開始會按照1000ms來等待，接著時間一到就把對應的函式-timeout放入至Callback Queue，然後等到Call Stack為空時，就把Queue中的timeout函式放到Main Thread來執行。
```
setTimeout(function timeout() {
    console.log('delay 1 sec');
}, 1000);

console.log('Hello!');
```
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636826224/blog/event/eventloop/setTimeout1sec_s5m432.gif)

### setTimeout 例子2
設定一個等待0秒才執行的計時器，雖然看形式上會是先於顯示Hello，但實際上瀏覽器還得呼叫API來建立worker thread執行定時器、並按照時間將對應函式放入Callback Queue、等待Call Stack為空才能正式執行，所以基本上會慢於顯示Hello。 
```
setTimeout(function timeout() {
    console.log('delay 0 sec');
}, 0);

console.log('Hello!');
```
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1636826230/blog/event/eventloop/setTimeout0sec_dkuv8i.gif)


## 參考資料
1. [Event dispatch and DOM event flow](https://www.w3.org/TR/DOM-Level-3-Events/#event-flow)
2. [event: pull vs. push](https://softwareengineering.stackexchange.com/questions/363397/how-does-an-event-listener-work)
3. [Concurrency vs Event Loop vs Event Loop + Concurrency](https://medium.com/@tigranbs/concurrency-vs-event-loop-vs-event-loop-concurrency-eb542ad4067b)
4. [Event Queue](https://gameprogrammingpatterns.com/event-queue.html)
5. [Event loop、Call Stack 與 Task Queue](https://gist.github.com/librarylai/de189c3b53a3c6a24d40f2f6a10ee08c)
6. [Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
7. [jserv 對於event loop + Thread pool的課程作業](https://hackmd.io/@jwang0306/sehttpd)