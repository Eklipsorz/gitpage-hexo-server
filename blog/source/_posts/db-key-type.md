---
title:  資料庫表格-key種類
date: 2022-04-07 00:02:17
tags:
 - Key
categories:
 - Database
---

## 前置知識：子集

### subset & superset
假設有兩個集合(A、B)，若集合A的任一元素都是集合B的元素，那麼就集合A為集合B的子集(subset)，而B就是A的超集(superkey)，記為A⊆B，讀作A包含於B。舉例來說集合B為\{B1, B2, B3, B4\}，那麼若集合A為集合B的子集，那麼集合A可以是

```
{B1}
{B1, B2}
{B1, B2, B3}
{B1, B2, B3, B4}
```

Note：
1. sub-和super-開頭的字都分別泛指某項事物在某種程度來說是次要、淺下、低下、從屬，而super則是指重要、超越、之上、包含。
2. sub和super 描述set的話就是指在被包含和包含，前者是指被包含的set，後者是指包含其他set的set

### proper subset
假設有兩個集合(A、B)，若兩者間的關係為A⊆B且A≠B的話，集合A就是集合B的真集合(proper subset)，以B為\{B1, B2, B3, B4\}的例子，那麼若集合A為集合B的真子集，那麼集合A可以是前面三個，最後一個由於會是A=B，所以不能夠算上它
```
{B1}
{B1, B2}
{B1, B2, B3}
// 下面是不符合規則
{B1, B2, B3, B4}
```


Note：
1. proper 在這指最主要、最重要、嚴格(正確)意義上的，在這描述集合是強調該集合是確實是它所描述。
> belonging to the main, most important, or typical part



參考資料：
- [集合基礎知識](https://www.itread01.com/content/1547202186.html)


## key type

### key && superkey
key 主要是用來識別每一個紀錄的獨特性，如同身分證可以識別每一個人的身份那樣，通常會是由表格或者relation上的欄位組合\{A1, A2,..., An\}所構成，而要成為key的條件為：
1. 這些欄位/屬性必須能夠功能上確定剩下欄位/屬性，也就是說同樣的欄位組合\{A1, A2,..., An\}只會對應同個紀錄，不會對應到兩個不同的紀錄。
> Those attributes functionally determine all other attributes of the relation. That is, it is impossible for two distinct tuples of R to agree on all of A1, A2,..., An

2. \{A1, A2,..., An\}中的所有真子集合中的組合(如只有A1和A2、或者是只有A1、A2、A3)不能夠功能上確定剩下屬性，換言之，只有完整的 \{A1, A2,..., An\}才能確定且在決定紀錄的key中是擁有最小欄位量的key
> No proper subet of {A1, A2,...., An} functionally determines all other attributes of R; i.e., a key must be minimal

若一組欄位集合能夠包含key的話，該欄位集合會稱作為superkey，或者稱key的超集，該集合中的鑰匙都會滿足於key的第一條件，不一定能夠滿足第二個條件，比如說一個表格擁有以下這幾個欄位

```
A1, A2, A3, A4,..., An
```

，其中A1和A2是key，那麼superkey就會是

```
{A1, A2}
{A1, A2, A3}
{A1, A2, A3, A4}
{A1, A2, A3, A4, A5}
{A1, A2, A3, A4, A5, A6}
.
.
{A1, A2, A3, A4, A5, A6,..., An}
```

超鍵本身包含著候選鍵(candidate key)、替代鍵(alternate key)、主鍵(primary key)、外鍵(foreign key)

### primary key && candidate key && alternate key 
主鍵(primary key)是由開發者或者資料庫管理系統從由key所構成的集合X中選定出來的key，是主要實際用來識別每一個紀錄的獨特性，同時間也必須要滿足以下限制：
  - 主鍵值必須能夠識別紀錄
  > Must uniquely identify the row;
  - 主鍵值不能夠NULL
  > cannot have NULL values;
  - 主鍵值不能一直被改變
  > Should not change over the time;
  - 主鍵值必須盡可能越短越好：通常會以主鍵當作資料的索引，主鍵越短，越能節省空間和查詢時間
  > Should be as short as possible.
集合X中的每一個key都會是候選鍵(candidate key)，會具有以下特色：
  - 滿足key的所有條件(識別和最小化)
  - 能夠被採納為主鍵的key，因爲這點而稱之為候選
另外當主鍵從集合X選出時，剩下未被選到的候選鍵就即為次要鍵(Secondary Key)或者替換鍵(Alternate key)

參考資料：
- [關聯資料表正規化(NORMALIZATION)--鍵,KEY](https://blog.kkbruce.net/2010/10/normalization-key.html)
- [What is the difference between Primary key and prime attribute?](https://stackoverflow.com/questions/22064977/what-is-the-difference-between-primary-key-and-prime-attribute)
- [Alternate Key in RDBMS](https://www.tutorialspoint.com/Alternate-Key-in-RDBMS)


### foreign key
在一個表格X或者relation X中的欄位組合X是指向或者是其他表格Y或者其他relation Y的主鍵，那麼其欄位組合X對於表格X來說，是個外部來的主鍵，簡稱為外鍵(foreign key)，用途是來讓表格X透過外鍵來找到對應表格Y的對應主鍵，並用連接(如JOIN)來延伸其他資訊從而構成兩個表格之間的一致性。

比如說：假設有兩個表格分別為住戶資料、預約疫苗，住戶資料的主鍵為身份證，預約疫苗的主鍵為序號，其中預約疫苗表格的身分證欄位是指向住戶資料的主鍵，因此會是預約表格的外主鍵或者外鍵，透過這個外鍵和Inner Join可以延伸對應的住戶資料，而結果會是最下面的合併結果
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1649260773/blog/database/key/foreign-key-example_ourovc.png)

