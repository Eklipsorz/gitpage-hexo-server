---
title: DB-NF-Concept
tags:
---

# 從第一正規化數到第三正規化

## 正規化是什麼？
1. Normalize 使某個事物回歸至它原本正常的狀態或者它常出現的狀態
2. Normalization是指回歸至正常或者常態的過程/動作
3. 前兩者提到的正規化所指的正常或者常出現的狀態會基於設定者來定義
4. 在資料庫當中，為了避免當資料庫執行CRUD時所發生的異常問題-即資料冗餘、資料不一致、刪除夾帶其他代表某事物之資訊的資料等，而定義出：
  - 哪一種表格形式可以讓資料庫系統避免上述問題的正規形式(Normal Form)
  - 如何從擁有異常現象的表格轉換為對應的正規形式，而這種轉換稱之為Normalizeation
5. 由於完整正規形式所需要做的轉換處理成本過於沈重，而將完整正規形式依據處理成本以及嚴格性而分成第一正規化、第二正規化、第三正規化、BCNF、第四正規化、第五正規化、第六正規化等等正規化等級，在這裏普遍只會到第一至三正規化。
6. 除了第一正規化以外，每一個正規化都必須遵守前一個正規化的需求，且**每一個正規化都有對應的正規形式以及如何轉換成正規形式的方法**。



## 異常(anomaly)
當資料存放至表格X並對表格X進行CRUD中的CUD時，總是會發生以下異常：

### 插入時異常 (Insert Anomalies)
當對表格X進行Create操作，也就是插入新紀錄至表格X所會有的異常，比如：

- 紀錄冗余：當插入新紀錄至表格X時，新紀錄部分欄位內容會與表格X的舊紀錄部分欄位內容重複，且這些欄位能夠單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y的角度來這些紀錄，等同表格X儲存多餘/冗余的資料，拿下面電影案例來說，當為了Star wars插入新的演員紀錄至movies時，該演員紀錄又夾帶著movies所擁有的相同電影資料，以定義電影來說，這份表格所儲存的star wars資訊是多餘的。

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648979225/blog/database/NF/insert-anomaly-example1_odbotx.png)

- 新紀錄的欄位內容缺失：當插入新紀錄至表格X時，新紀錄部分欄位內容會是空的，對於資料庫系統來說，無法透過透過該欄位進行特定資料的存取，所以為了解決這點，資料庫系統會只允許欄位都有填寫的紀錄新增至表格。這造就兩類問題：一類是允許為空但會使資料庫系統無法透過該欄位做太多運用，另一類則是不允許為空但這樣就無法紀錄該資訊。
  
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648979225/blog/database/NF/insert-anomaly-example2_dti3qp.png)

### 更新時異常 (Update Anomalies)
當對表格X的某紀錄進行Update操作，也就是變更表格X的某筆紀錄之內容所會發生的異常，比如：

- 更新後的資料不一致問題：當對表格X的某份紀錄進行Update操作時，若這份紀錄的部分欄位內容是存在多筆紀錄上的話，且這些欄位能夠單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y來說的話，這些定義著Y的紀錄會出現定義資訊不一致的問題，如定義同個電影的紀錄有兩筆，但經過修改後，現在同個電影出現兩個不同的電影種類：SciFi和Drama

![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648980582/blog/database/NF/update-anomaly-example1_azdu1t.png)

### 刪除時異常 (Deletion Anomalies)
當對表格X的某紀錄進行Delete操作，也就是刪除表格X的某筆紀錄之內容所會發生的異常，比如：

- 當紀錄一被刪除時，可能會帶刪除定義其他事物的欄位資訊：當對表格X的某份紀錄進行Delete操作時，若這份紀錄的部分欄位內容是能單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y來說的話，只要這些欄位隨著紀錄被刪除而刪去的話，那麼就無從定義事物Y。比如說當要刪去演員為Vivien Leigh的紀錄時，會連帶刪除它夾帶的電影資訊-Gone With the Wind。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1648988727/blog/database/NF/delete-anomaly-example1_q8isex.png)

https://liuzhian.github.io/2018/11/15/数据库-正规化/

## 總結：異常
從對資料庫的紀錄所做出的CRUD操作衍生出的異常案例來看，可以大致將異常分成資料冗餘(Data Redundancy)、資料本身的完整性異常(包含資料一致性、刪除資料會導致永久失去另一個潛藏的資訊紀錄)

### Data Redundancy 
資料冗餘是指資料紀錄本身的冗餘、重複情況，換言之，擁有資料冗余問題的資料，其內容本身是毫無意義(無法代表其資料本身、無法替資料庫管理系統進行額外處理等等)或者與其他資料紀錄內容是一樣的，在涉及到資料的資料庫中，常見的情況會有：
  - 在同一個表格下存在著多筆紀錄是重複的
  - 在同一個表格下有筆紀錄欄位是重複存在多個值。
  - 同個欄位重複出現在多個表格。
> While different in nature, data redundancy also occurs in database systems that have values repeated unnecessarily in one or more records or fields, within a table, or where the field is replicated/repeated in two or more tables. 

這項問題可能會帶來以下負面影響，主要會依據毫無意義和重複再進一步推敲：
 - 資料上的不一致：相同資料在不同地方不能夠匹配到相同的結果
 - 多浪費資料的儲存空間去存放重複、冗餘的資料：同一欄位儲存多個值、同一列儲存過多的欄位值、儲存無法匹配的冗餘資訊，前兩者如拿儲存無限多數量的資訊來在同一個欄位儲存或者同一列上的欄位數量來儲存，會使整個表格設計都要被迫以最大數量來定義每一個欄位能存多少以及欄位數量要多少，否則系統無法負荷，若以動態調整最大值則需要讓資料庫管理系統進行額外計算才能達成

Note: 
1. Redundant 中文為冗餘、多餘的，主要是強調某事物在某些場合是以非必要的形式(重複)出現
> Redundant：exceeding what is necessary or normal、characterized by similarity or repetition
2. Redundancy 是指冗餘、多餘的程度/狀況，而Data Redundancy是指資料本身會出現的冗餘狀況，比如出現同一個場合出現重複或類似的資料。
> Redundancy：The quality or state of being redundant
3. 參考資料為
- [Data Redundancy](https://en.wikipedia.org/wiki/Data_redundancy)


### Data Integrity
資料完整性(Data Integrity) 是指 **實際儲存的資料紀錄 與 按照預先定義好的規則而建立的資料紀錄 之間的相似性，越高就越一樣，同時代表著越完整**，換言之資料庫管理系統會使用預先定義好的規則來檢驗實際儲存的資料紀錄是否合法，而這裡預先定義好的規則是指資料庫管理系統對於資料的紀錄規則以及開發者自行定義好的紀錄規則，規則比如設定不允許已經紀錄好的紀錄出現欄位空值的情況、不允許儲存空欄位值的紀錄
> refers to the accuracy and consistency of data stored in a database, data warehouse, data mart or other construct
> ensure data is recorded exactly as intended (such as a database correctly rejecting mutually exclusive possibilities)

若違背定義好的規則，那麼就即為資料完整性的異常，在這裡可能會有的異常會是：
  - 已經儲存資料庫的紀錄出現欄位空值的現象：這使得該欄位無法被資料管理系統作為索引、其他應用的欄位，即為毫無意義
  - 刪除資料可能會導致永久失去另一個潛藏的資訊紀錄：當對表格X的某份紀錄進行Delete操作時，若這份紀錄的部分欄位內容是能單獨定義某些事物Y(如能夠定義餐廳的欄位)，從定義事物Y來說的話，只要這些欄位隨著紀錄被刪除而刪去的話，那麼就無從定義事物Y

Note: 
1. integrity: 完整或者整體的程度，在這裏會以實物和預期實物之間的相同程度作為比較
> the quality of being whole and complete
2. 參考資料為
- [数据完整性](https://zhuanlan.zhihu.com/p/345372629)
- [Data integrity](https://en.wikipedia.org/wiki/Data_integrity)

### Data consistency
1. 意指相同資料在不同地方是否能夠匹配到相同的結果
>  refers to whether the same data kept at different places do or do not match.
2. 在關聯式資料庫中，是指同一個關係R上相同的屬性集合A是否能夠匹配相同對應的屬性集合B，若出現相同的屬性集合A匹配到不相同的對應屬性集合B，代表其Data consistency就很差，反之，若同一個關係R，每個紀錄都能透過相同的屬性集合A來對應相同的對應屬性集合B，代表Data consistency就很好
3. Data consistency若太差的話，很容易在資料處理上的CRUD出現異常情況。




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


## 1NF-第一正規化
1. 該正規化主要解決資料冗餘的問題，其格式定義為**在同個資料表格內，每個欄位/屬性的內容都會是不可再分割的原子值(Atomic Value)**，而原子值是強調著內容不可分割，並非指的是現實中的原子。
2. 若資料表格滿足上述定義，就為第一正規化表格
3. 若遇到不滿足於其規格的話，比如同一個表格下的屬性出現複合屬性(Composite attribute)、同一個屬性出現多筆資料，會以下面形式來轉換：
  - 同一個表格下的屬性出現複合屬性(Composite attribute)：由於複合屬性本身由多個屬性組成，所以本身違反著不可再分割這性質，而為了轉換第一正規化格式表格，可以先從複合屬性切割出那些屬性，並刪掉複合屬性名稱就能解決，拿下面表格來說明的話，可將身為複合屬性的地址屬性切割成街道、城市、郵遞區號，然後再刪掉地址這屬性名稱
  ```
  // 變更前：
  識別碼 | 地址 | 電話

  // 變更後
  識別碼 | 街道 | 城市 | 郵遞區號 | 電話
  ```
  - 同一個屬性值出現多筆資料：多筆資料的存在本身就違反著每個欄位/屬性的內容都會是不可再分割的原子值這性質，為了轉換第一正規化格式表格，可以先以每一筆紀錄的每個欄位都只有一個單一值為原則將多餘的紀錄搬移出來變成新的紀錄，若同一個紀錄下的其他屬性是單一值，就在新的紀錄上填上單一值，那下面紀錄來說的話，由於紀錄1的電話有兩筆，所以會建立新的紀錄來存放多餘的電話，其餘屬性值則由於原本就是單一值，所以就直接沿用原值，新的紀錄就為紀錄2
  ```
  // 變更前：
  紀錄1:
    識別碼: 1
    電話: 0912345678, 0912345679
    標題: HI
  // 變更後
  紀錄1:
    識別碼: 1
    電話: 0912345678
    標題: HI
  
  紀錄2:
    識別碼: 1
    電話: 0912345679
    標題: HI
  ```
4. 補充資料：
  - 複合屬性(Composite attribute)：由多個屬性組成的單一屬性，比如地址屬性本身可包含著街道、城市、郵遞區號等屬性值所構成。
  > An attribute composed of many other attribute

## 第二正規化

## 第三正規化