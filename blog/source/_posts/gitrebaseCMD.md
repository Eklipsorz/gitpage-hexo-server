---
title: git rebase && undo rebase
tags:
  - git
  - rebase
categories:
  - 深入淺出
date: 2021-09-29 14:41:21
---


# 進階指令 - git rebase
在這裡base會是指以什麼做為根基，而在git系統是指以什麼commit紀錄(節點)作為分支的起始點，拿下圖來說的話，分支1(Branch 1)是以Master/Main分支上的commitM來作為它的base，而re是重新、再一次，re和base組合在一起就是重新定義目前分支的起始點，換言之就是把某個分支的base改換另一個commit節點來作為新的base。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632815679/blog/git/rebase/anExampleOfigtbase_ybh3kf.png)

在這裡根據它所定義的意義來看，它擁有兩種主要功能：1. 將同一個分支上的多個commit節點合併成一個commit節點，2. 將分支A複製至另一個分一個分支B的頂端紀錄(分支B的最新提交紀錄)上。

## 合併多個commit節點
當想要讓同一分支上的多個commit節點合併成一個commit節點時，可以下達以下指令：
```
git rebase -i <sha-1>
```
接著它會以當前分支上的最新提交紀錄至sha-1對應版本的後一版作為處理範圍，接著以這個範圍的提交紀錄顯示在vim編輯器，內容格式如下，第一列會顯示sha-1對應版本的後一版，第二列則是後二版，依此類推至最後一行為最新版本，每一個版本的第一欄command是表示rebase對於該版本所要做的處理，後面欄位則是顯示對應版本的sha-1碼、提交訊息。

```
command <sha-1> commit message 1    
              .
              .
              .
command <sha-1> commit message N-1
command <sha-1> commit message N
```

command部分預設會是pickup，代表rebase不會變動該提交紀錄，但如果要合併的話，可以改成s或者壓扁(squash)，這指定會將前一個版本合併成同一個版本，比如說在版本N的第一欄填上squash，那麼rebase會將版本N和版本N-1壓扁成一塊提交紀錄。

```
pickup <sha-1> commit message N-1
squash <sha-1> commit message N
```

而壓扁可以不只可以壓2個版本，還能按照squash數量以及能被壓扁的量來盡可能增加可壓的數量，比如說在版本N至版本M+1前面填上squash，壓扁版本N至版本M成一個提交紀錄。

```

pickup <sha-1> commit message M
squash <sha-1> commit message M+1
              .
              .
              .
squash <sha-1> commit message N-2
squash <sha-1> commit message N-1
squash <sha-1> commit message N
```

不論你壓扁了多少個，最後需要你填寫最新版本的提交訊息，這時你在按照自己所需來填寫版本，並且最後結果會以你壓扁後的版本紀錄為主，但實際上原本壓扁前的版本紀錄也會存在git系統直到被git回收機制被回收，在這裡根據壓扁的位置我們可以大致區分為壓扁最新幾筆紀錄以及壓扁中間幾筆紀錄，以這兩個情況下我們試著用一些簡單例子說明當壓扁後來說明會是怎麼樣的情形，接著簡化成一種通用概念來表示。


### 壓扁最新幾筆版本紀錄
我們以一個簡單例子來說明，首先我們先提交五個版本紀錄，每一個版本都附加著sha-1碼，並且提交順序是以序號1的大小來決定，數字越大代表提交順序是越後面，HEAD因此指向第五個版本來表示目前分支最新版本是第五個版本，

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632837434/blog/git/rebase/simpleExample_zymlon.png)

在這裡我們想要讓最新的三筆版本紀錄壓扁成(合併成)一個版本，在下達git rebase相關指令後，其節點圖會是如下圖所示，實際能看到會是由原來第三版本至第五版本(commit 3 ~ commit 5)壓扁而成的最新第三版本(commit 3')，也就是目前HEAD所指向的，而它的sha-1碼會因為壓扁的關係而改變，然而git系統會保留壓扁前的節點圖，也就是內部隱藏的部分(使用橘框圍住的部分)，這部分可以幫助開發者回溯到壓扁前的節點圖，這邊會由後續的取消方法來說明。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632847969/blog/git/rebase/simpleExampleAfterSquash_licvyf.png)

若我們將這個具體例子轉換成通用概念的話，會考慮著有N個版本紀錄，第N版本原本是HEAD所指向的，第M版本至第N版本則是指定被壓扁的對象，那麼在壓扁後的節點圖會像是如下圖，最新的N-M+1個版本會壓扁成一個新的版本，也就是下圖中的第M'個版本，同樣地，這只是實際上呈現的節點圖且它的sha-1碼會因為版本內容不同以及壓扁的關係而改變，而內部隱藏(橘框圍住的節點們)存放著原本壓扁前的節點圖。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632848199/blog/git/rebase/generalExampleAfterSquash_frz7ry.png)

### 壓扁中間幾筆版本紀錄
同樣地，我們再次拿上面的簡單例子來壓扁，順序和HEAD都一樣，但這次是壓扁第二～三的版本紀錄。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632837434/blog/git/rebase/simpleExample_zymlon.png)

當我們下達git rebase的指定後，其節點圖會是如下圖所示的實際呈現那邊，在那裡會是由原第二～三個版本節點(commit 2 ~ commit 3)壓扁後的版本節點2'，接著再從原節點圖複製第四～五個版本節點並與新節點2'進行連接，所以下面的每個版本節點所擁有sha-1都經由複製和壓扁而改變，當然，原本還沒壓扁的節點圖(以橘框圍著的節點們)會留起來等待git回收機制進行回收或者讓開發者回溯到壓扁前的節點圖
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632848381/blog/git/rebase/simpleExample2AfterSquash_eztpbr.png)

若我們將這個具體例子轉換成通用概念的話，考慮著N個版本紀錄以及第N版本原本是HEAD所指向的，第X至X+Y個版本節點(commit X至commit X+Y)則是被壓扁成一個版本節點的對象，那麼在壓扁後的節點圖會像是如下圖中的實際呈現那樣，第X至X+Y個版本節點(commit X至commit X+Y)被壓扁成第X'個版本節點，而原節圖點上第X+Y個節點(commit X+Y)之後的節點會被複製至下面與新節點X'進行連接，這些節點同樣地因為壓扁和複製而被更改sha-1碼，而git系統會隱藏著壓扁前的節點圖，也就是橘框圍住的範圍。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632848713/blog/git/rebase/generalExampl2eAfterSquash_ptebas.png)

### 取消方法

若你後悔這次合併後的結果，可以試著利用git reflog找出壓扁前的HEAD節點(會以sha-1碼代表)，接著透過git reset讓HEAD去指向壓扁前的版本，而這個動作相當於你將版本還原至壓扁前的版本，然而你壓縮後的版本節點圖不會因此而消失，會如同壓扁後的內部隱藏的那樣，若你突然想從壓縮前回溯至壓縮後的版本，只需要知道當時的HEAD對應的sha-1碼就行。

```
git reflog
git reset --hard <sha-1>
```

如果拿 "壓扁最新幾筆版本紀錄" 和 "壓扁中間幾筆版本紀錄" 所提到的例子來取消/還原的話，只需要各從中獲取原本壓扁前的最新提交版本(sha-1)，在這裡就是圖中內部(隱藏)藍框圍住的節點，

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632848919/blog/git/rebase/UndoDiagram1_eavjlx.png)
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632848920/blog/git/rebase/UndoDiagram2_agglxa.png)


只要取得被選取到版本，並下達以下指令：
```
git reset --hard <sha-1> N
```

就能將他們分別還原成下面的二張圖，第一張圖對應上面的第一張，也就是壓扁最新幾筆版本紀錄前後的結果，第二張圖對應著上面的第二張圖，也就是壓扁中間幾筆版本紀錄前後的結果，可以看到還原後，git系統會內部儲存(隱藏)壓扁後的節點圖，也就是橘框圍住的節點們，若要再從壓扁前還原至壓扁後的狀態，只需要reset至下圖中藍框圍住的節點所擁有的sha-1碼就能達成。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632891320/blog/git/rebase/UndoDiagram1Result_n2nzyc.png)
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632891320/blog/git/rebase/UndoDiagram2Result_yiulfd.png)

### 例子
假設我們提交五個版本，讓這五個版本連成一條節點圖：
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632841538/blog/git/rebase/anUndoExample_ctj0o4.png)

接著我們壓扁所有跟master有關的提交版本後，兩個被壓扁的版本訊息會是add all masters，而其節點圖會呈現如下，你可以看到在這個版本和在他前面版本所擁有sha-1碼全都改變，這代表著這些節點已經經過被複製或者壓扁的處理才改變的。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632841538/blog/git/rebase/anUndoExampleAfterUndoing_xbc0kt.png)

這時可以利用git reflog去查閱過去對節點做了哪些改變，左側是被處理的版本號碼，而右側是實際的處理內容，找到rebase訊息之前的提交紀錄，會看到最近的一筆為eabd0dc對應的版本，而這個版本剛好是原本壓扁前的版本紀錄，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632841538/blog/git/rebase/anUndoExampleReflog_dfnq6b.png)

這時只要下達以下指令就能還原成壓扁前的節點圖：

```
git reset --hard eabd0dc
```
## 將分支複製轉移至另一個分支

當想要將分支A複製至另一個分一個分支B的頂端紀錄(分支B的最新提交紀錄)上時，可以先透過git checkout轉移至分支A，


![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632849316/blog/git/rebase/RebaseToBranchDiagram_uy6taw.png)


然後對它下達以下指令：
```
git rebase branchB
```

使分支A下的所有節點將會在分支B的最新提交紀錄上面，由於轉移過程是直接從分支A複製並讓分支B的最新提交版本去連接複製過來的版本紀錄，所以每個被複製過來的版本所擁有的sha-1都被改變，而剩餘不在分支A的版本節點都維持一樣的sha-1，皆未曾改變過，而HEAD由於是從分支A來下達rebase，所以HEAD會繼續指向分支A的最新紀錄，同樣地，git系統會在內部儲存(隱藏)原本分支A的節點圖來幫助開發者還原或者等著被git系統回收，在這裡以橘框來表示

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632849315/blog/git/rebase/RebaseToBranchDiagramAfter_hqfsgo.png)


另外分支B和分支A仍然是兩個獨立的分支，只是分支A包含分支B目前的所有節點，而分支B的版本節點仍維持rebase之前的內容，其節點圖如下圖：
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632844205/blog/git/rebase/BranchB_b1sdvu.png)

而分支A的節點圖會是：

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632844205/blog/git/rebase/BranchA_ukxjof.png)


### 取消方法
若你後悔這次分支A的rebase結果，由於本身是分支A移動至分支B，而分支B本來就沒改變，所以得從分支A著手取消掉這次rebase的結果，首先git checkout至分支A，並且下達git reflog找出該分支A在rebase前的最新版本紀錄所對應的sha-1碼，並且對其sha-1碼下達git reset
```
git checkout branchA
git reflog
```

在這裡rebase前的最新版本紀錄會是以下圖中藍框圍住的節點，
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632890268/blog/git/rebase/UndoDiagram3_ouup50.png)

從那取得sha-1碼，並且下達下面指令
```
git reset --hard <sha-1>
```

就能使分支A恢復成rebase之前的分支A，也就是下圖中實際呈現的那樣，然而git系統還是會保留(隱藏)rebase之後的分支A等待著回收或者還原，若要再從rebase之前還原至rebase之後的狀態，只需要reset至下圖中藍框圍住的節點所擁有的sha-1碼就能達成
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632890688/blog/git/rebase/UndoDiagram3Result_okbp3a.png)


### 例子
假設我們建立了三個分支，分別為cat、sloth、master，master是主分支，目前儲存master1、master2這兩個檔案並依序提交了f10a1f8和306cf9d這兩個對應版本，而cat、sloth則是從master主分支上的306cf9d最新版本而衍生過來的額外分支，所以這兩個分支包含主分支的306cf9d版本內容以及它們本身就有的檔案內容，比如cat分支就有cat1、cat2，而sloth分支則是有sloth1、sloth2這兩份檔案。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632896079/blog/git/rebase/UndoRebaseExample_xxnrys.png)

現在我們想要讓sloth分支搬移至cat分之之上，那麼就會先checkout至sloth分支讓HEAD指向sloth，並下達git rebase cat讓節點圖變成下圖這樣，此時的HEAD仍指向著sloth，sloth分支會包含著cat分支下的所有版本紀錄，而cat分支和master主分支的內容仍維持rebase之前的版本內容，另外你可以那下面圖和上面圖比對一下每一個版本紀錄所代表的sha-1碼，會發現只有分支sloth的節點所擁有sha-1被改變，這代表著他們是經由複製並搬移至分支cat最新紀錄之上。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632896079/blog/git/rebase/RebaseExample_lsfkkl.png)

若我們想要著手想要讓sloth分支恢復到rebase之前的內容，那麼只需要checkout至sloth分支並下達git reflog去查閱該分支下在rebase前的最新紀錄所擁有的sha-1碼是多少，從下圖中，左側是被處理的版本號碼，而右側是實際的處理內容，首先我們可以先看到rebase的訊息，再往下看就可以看到sloth原本提交的內容，而最前面的提交內容b8faf8f就是原本sloth分支的最新版本內容。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1632896081/blog/git/rebase/UndoRebaseReflog_fnssnx.png)


所以只要下達以下指令就能使sloth分支還原至第一張圖那樣。

```
git reset --hard b8faf8f
```

