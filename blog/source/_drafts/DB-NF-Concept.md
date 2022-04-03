---
title: DB-NF-Concept
tags:
---

# 從第一正規化數到第三正規化

## 正規化是什麼？
Normalize 使某個事物回歸至它原本正常的狀態或者它常出現的狀態，Normalization是指回歸至正常或者常態的過程/動作，正常或者常出現的狀態會基於設定者來定義

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
  - 在同一個表格下的每一個紀錄下之同一個欄位值出現多筆資料，比如說某個紀錄下的欄位值出現多個資料
  - 在同一個表格下的單一屬性本身是由多個屬性組合而成，比如地址屬性可以包含縣市、街道、州郡、國家等這些屬性
  - 同個欄位重複出現在多個表格
> While different in nature, data redundancy also occurs in database systems that have values repeated unnecessarily in one or more records or fields, within a table, or where the field is replicated/repeated in two or more tables. 
4. Data Redundancy 會造成的缺失有：
  - 浪費資料庫的儲存空間去存放重複性的資料
  - 資料過於繁雜而產生資料維護上的問題

## Data consistency
1. 意指相同資料在不同地方是否能夠匹配到相同的結果
>  refers to whether the same data kept at different places do or do not match.
2. 在關聯式資料庫是指在同一個關係R中，相同的屬性集合A是否能夠匹配相同對應的屬性集合B，若出現相同的屬性集合A匹配到不相同的對應屬性集合B，代表其Data consistency就很差，反之，若同一個關係R，每個紀錄都能透過相同的屬性集合A來對應相同的對應屬性集合B，代表Data consistency就很好
3. Data consistency若太差的話，很容易在資料處理上的CRUD出現異常情況。

### 異常(anomaly)
當資料存放至表格X並對表格X進行CRUD中的CUD時，總是會發生以下異常：
1. 插入時異常 (Insert Anomalies)：當對表格X進行Create操作，也就是插入新紀錄至表格X所會有的異常
  - 紀錄冗余：當插入新紀錄至表格X時，新紀錄部分欄位內容會與表格X的舊紀錄部分欄位內容重複，且這些欄位能夠單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y的角度來這些紀錄，等同表格X儲存多餘/冗余的資料，拿下面電影案例來說，當為了Star wars插入新的演員紀錄至movies時，該演員紀錄又夾帶著movies所擁有的相同電影資料，以定義電影來說，這份表格所儲存的star wars資訊是多餘的。
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648979225/blog/database/NF/insert-anomaly-example1_odbotx.png)
  - 新紀錄的欄位內容缺失：當插入新紀錄至表格X時，新紀錄部分欄位內容會是空的，對於資料庫系統來說，無法透過透過該欄位進行特定資料的存取，所以為了解決這點，資料庫系統會只允許欄位都有填寫的紀錄新增至表格。這造就兩類問題：一類是允許為空但會使資料庫系統無法透過該欄位做太多運用，另一類則是不允許為空但這樣就無法紀錄該資訊。
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648979225/blog/database/NF/insert-anomaly-example2_dti3qp.png)

2. 更新時異常 (Update Anomalies)： 當對表格X進行Update操作，也就是變更表格X的某筆紀錄之內容所會發生的異常
  - 更新後的資料不一致問題：當對表格X的某份紀錄進行Update操作時，若這份紀錄的部分欄位內容是存在多筆紀錄上的話，且這些欄位能夠單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y來說的話，這些定義著Y的紀錄會出現定義資訊不一致的問題，如定義同個電影的紀錄有兩筆，但經過修改後，現在同個電影出現兩個不同的電影種類：SciFi和Drama
  ![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648980582/blog/database/NF/update-anomaly-example1_azdu1t.png)
3. 刪除時異常 (Deletion Anomalies)：



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