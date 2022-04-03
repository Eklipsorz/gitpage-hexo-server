---
title: DB-NF-Concept
tags:
---

# 從第一正規化數到第三正規化

## 正規化是什麼？
1. Normalize 使某個事物回歸至它原本正常的狀態或者它常出現的狀態
2. Normalization是指回歸至正常或者常態的過程/動作
3. 前兩者提到的正規化所指的正常或者常出現的狀態會基於設定者來定義
4. 在資料庫當中，為了避免當資料庫執行CRUD時所發生的異常-即資料冗余、資料不一致、刪除夾帶其他代表某事物之資訊的資料等，而定義出：
  - 哪一種表格形式可以讓資料庫系統避免上述問題的正規形式(Normal Form)
  - 如何從擁有異常現象的表格轉換為對應的正規形式，而這種轉換稱之為Normalizeation
5. 由於完整正規形式所需要做的處理成本過於沈重，而將完整正規形式依據處理成本以及嚴格性而分成第一正規化、第二正規化、第三正規化、BCNF、第四正規化、第五正規化、第六正規化等等正規化等級，在這裏普遍只會到第一至三正規化。
6. 除了第一正規化以外，每一個正規化都必須遵守前一個正規化的需求，且**每一個正規化都有對應的正規形式以及如何轉換成正規形式的方法**。

## 解決了什麼？

## n-Tuple / Tuple 
1. 一個Tuple是由n個有限元素所組成的序列對，比如以下的單一序列對會容納著n個有限元素
```
{1, 2, 3,....., n}
```


## Data redundancy 
1. Redundant 中文為冗餘、多餘的，主要是強調某事物在某些場合是以非必要的形式重複出現
> Redundant：exceeding what is necessary or normal、characterized by similarity or repetition
2. Redundancy 是指冗餘、多餘的程度/狀況，而Data Redundancy是指資料本身會出現的冗餘狀況，比如出現同一個場合出現重複或類似的資料。
> Redundancy：The quality or state of being redundant
3. 由於 Data Redundancy 是出現在資料本身的冗餘狀況，所以涉及到資料的資料庫系統也會出現Data Redundancyg的問題，比如：
  - 在同一個表格下存在著多筆紀錄是重複的
  - 在同一個表格下有筆紀錄欄位是重複存在多個值。
  - 同個欄位重複出現在多個表格。
> While different in nature, data redundancy also occurs in database systems that have values repeated unnecessarily in one or more records or fields, within a table, or where the field is replicated/repeated in two or more tables. 
4. Data Redundancy 會造成的缺失有：
  - 浪費資料庫的儲存空間去存放重複性的資料
  - 資料過於繁雜而產生資料維護上的問題

## Data consistency
1. 意指相同資料在不同地方是否能夠匹配到相同的結果
>  refers to whether the same data kept at different places do or do not match.
2. 在關聯式資料庫中，是指同一個關係R上相同的屬性集合A是否能夠匹配相同對應的屬性集合B，若出現相同的屬性集合A匹配到不相同的對應屬性集合B，代表其Data consistency就很差，反之，若同一個關係R，每個紀錄都能透過相同的屬性集合A來對應相同的對應屬性集合B，代表Data consistency就很好
3. Data consistency若太差的話，很容易在資料處理上的CRUD出現異常情況。

### 異常(anomaly)
當資料存放至表格X並對表格X進行CRUD中的CUD時，總是會發生以下異常：
1. 插入時異常 (Insert Anomalies)：當對表格X進行Create操作，也就是插入新紀錄至表格X所會有的異常
  - 紀錄冗余：當插入新紀錄至表格X時，新紀錄部分欄位內容會與表格X的舊紀錄部分欄位內容重複，且這些欄位能夠單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y的角度來這些紀錄，等同表格X儲存多餘/冗余的資料，拿下面電影案例來說，當為了Star wars插入新的演員紀錄至movies時，該演員紀錄又夾帶著movies所擁有的相同電影資料，以定義電影來說，這份表格所儲存的star wars資訊是多餘的。
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648979225/blog/database/NF/insert-anomaly-example1_odbotx.png)
  - 新紀錄的欄位內容缺失：當插入新紀錄至表格X時，新紀錄部分欄位內容會是空的，對於資料庫系統來說，無法透過透過該欄位進行特定資料的存取，所以為了解決這點，資料庫系統會只允許欄位都有填寫的紀錄新增至表格。這造就兩類問題：一類是允許為空但會使資料庫系統無法透過該欄位做太多運用，另一類則是不允許為空但這樣就無法紀錄該資訊。
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648979225/blog/database/NF/insert-anomaly-example2_dti3qp.png)

2. 更新時異常 (Update Anomalies)： 當對表格X的某紀錄進行Update操作，也就是變更表格X的某筆紀錄之內容所會發生的異常
  - 更新後的資料不一致問題：當對表格X的某份紀錄進行Update操作時，若這份紀錄的部分欄位內容是存在多筆紀錄上的話，且這些欄位能夠單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y來說的話，這些定義著Y的紀錄會出現定義資訊不一致的問題，如定義同個電影的紀錄有兩筆，但經過修改後，現在同個電影出現兩個不同的電影種類：SciFi和Drama
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648980582/blog/database/NF/update-anomaly-example1_azdu1t.png)
3. 刪除時異常 (Deletion Anomalies)： 當對表格X的某紀錄進行Delete操作，也就是刪除表格X的某筆紀錄之內容所會發生的異常
  - 當紀錄一被刪除時，可能會帶刪除定義其他事物的欄位資訊：當對表格X的某份紀錄進行Delete操作時，若這份紀錄的部分欄位內容是能單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y來說的話，只要這些欄位隨著紀錄被刪除而刪去的話，那麼就無從定義事物Y。比如說當要刪去演員為Vivien Leigh的紀錄時，會連帶刪除它夾帶的電影資訊-Gone With the Wind。
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648988727/blog/database/NF/delete-anomaly-example1_q8isex.png)

异常（anomaly)
确保数据之间的依赖关系是合乎逻辑的：
  - 
https://liuzhian.github.io/2018/11/15/数据库-正规化/


解決Data redundancy
https://en.wikipedia.org/wiki/Data_redundancy
 
改善 data integrity

https://en.wikipedia.org/wiki/Data_integrity

使資料庫維護更容易




What is Normalisation (or Normalization)?
https://stackoverflow.com/questions/246701/what-is-normalisation-or-normalization


如何理解关系型数据库的常见设计范式？
https://www.zhihu.com/question/24696366


数据库第一二三范式到底在说什么？
https://zhuanlan.zhihu.com/p/20028672

为什么说“ 一个DBA是否有足够的设计能力，就看他有多大的能力做反范式设计就可以了，不要问为什么”？
https://www.zhihu.com/question/19900437/answer/14089402

MySQL中資料中設計中的正規化與反正規化(看文字就行)
https://www.itread01.com/content/1545374341.html
## 為啥目前正規化要基於前一個正規化來做