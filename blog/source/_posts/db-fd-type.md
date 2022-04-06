---
title: Functional Dependency type
date: 2022-04-06 23:38:48
tags:
 - Functional Dependency
categories:
 - Database
---

# Functional Dependency type


## Functional dependency

在同個表格X或者relation X上，該表格X擁有的屬性集合分別為屬性集合A和屬性集合B，若任兩筆紀錄在屬性集合A上是擁有相同屬性值集合X'，那麼這兩筆紀錄在屬性集合B也呈現出另一種相同的屬性值集合Y'的話，那麼就能以Y' = f(X')來表示同個屬性值集合X'能夠透過某函式對應(確定)另一個相同屬性值集合Y'是什麼，不會出現同個屬性集合值X'對應不同的屬性值集合，藉由這點也可以延伸成屬性集合A可以在某函式對應(確定)屬性集合B。
> If two tuples of R agree on all of the attributes A1, A2,..., An (i.e., the tuples have the same values in their respective components for each of these attributes), then they must also agree on all of another list of attributes B1, B2, ..., Bm. We write this FD formally as 
```
A1, A2,..., An -> B1, B2,..., Bm
// say that
A1, A2,..., An functionally determine B1, B2,..., Bm
```

這時會寫作成以下形式，其描述為屬性集合A在函式上確定屬性集合B、屬性集合A在功能方面上確定屬性集合B、屬性集合B藉由函式而依賴於屬性集合A
```
A -> B
A functionally determine B
```
參考資料：
1. A first course in database systems (3rd)
## Full functional dependency
在同個表格中，若欄位集合X -> 欄位集合Y 且 對於 X 的任意一個真子集 X'而言， X' -> Y 是不能夠成立，那麼就表示只有
完整的欄位集合X能夠對應另一個欄位集合Y，不能夠將欄位集合X切割成各個子集合，然後也無法讓每個子集合去對應欄位集合Y，因此稱欄位集合Y函式依賴於完整的的欄位集合X，這時函式依賴會是Full functional dependency

> An attribute is full functional dependent on another attribute, if it is Functionally Dependent on that attribute and not on any of its proper subset.

舉例來說：
假設有一個表格上具有學號、班級、姓名這三個欄位，其中不同班級下是存在學號相同，而班級內是不存在學號相同的，那麼在這裡學號和班級會函式決定姓名，也就是上述提到的欄位集合X會是(學號, 班級)，而欄位集合Y會是姓名，其中學號由於不同班級下的學號相同而不能夠單獨函式決定姓名，班級也是無法單獨函式決定，因此姓名和(學號, 班級)間得函式依賴會是完全函式依賴

參考資料：
- [Full-functional-dependency-in-DBMS](https://www.tutorialspoint.com/Fully-functional-dependency-in-DBMS)

## Partial functional dependency
在同個表格中，若欄位集合X -> 欄位集合Y 且 對於 X 的任意一個真子集 X'而言， X' -> Y 是可以成立，那麼就表示欄位集合X中的一部分是可確定欄位集合Y 或者說 欄位集合Y函式依賴於欄位集合X的一部分子集合，這時的函式依賴會是Partial functional dependency

舉例來說：
假設有一個表格上具有學號、身份證、姓名這三個欄位，那麼在這裡的函式依賴會是(學號, 身分證)決定姓名，換言之，這裡欄位集合X會是(學號, 身份證)以及欄位集合Y會是姓名，若對欄位集合X進行進一步的切割，如學號 或者 身分證，那麼這兩個任一個都能因為獨特性來確定姓名，因此姓名和(學號, 身分證)之間的函式依賴是部分函式依賴。

也就是上述提到的欄位集合X會是(學號, 班級)，而欄位集合Y會是姓名，其中學號由於不同班級下的學號相同而不能夠單獨函式決定姓名，班級也是無法單獨函式決定，因此姓名和(學號, 班級)間得函式依賴會是完全函式依賴


參考資料：
- [Differentiate between Partial Dependency and Fully Functional Dependency](https://www.geeksforgeeks.org/differentiate-between-partial-dependency-and-fully-functional-dependency/)

## Transitive functional dependency
在同一個表格中，若有欄位集合A能函式決定函式集合B，且函式集合B也能函式決定欄位集合C，在這樣的結果下，欄位集合A可透過函式集合B而間接函式決定欄位集合C的形式，但為了嚴格定義A、B、C 皆為不同的欄位集合且彼此間無任何延伸關係(e.g., 比如不能透過A來延伸成B)：A和B實際上是源自同一個欄位集合、B和C實際上源自於同一個欄位集合的問題，為了避免這件事而特別設定以下嚴格條件

```
// 避免(x1, x2) -> x3, x4, x5 且 (x1, x2, x3) -> x4, x5
// A: (x1, x2) 
// B: (x1, x2, x3)
// C: (x4, x5)
// 實際上 A 和 B 都源自於同一個欄位集合(x1, x2)
// 以及
// 避免(x1, x2) -> x3, x4, x5 且 (x1, x2, x3) -> x4, x5
// A: (x1, x2, x3)
// B: (x1, x2)
// C: (x4, x5)
// 實際上 A 和 B 都源自於同一個欄位集合(x1, x2)
1. B !-> A (B 不能函式決定 A) 
// 等同上方
2. C !-> B (C 不能函式決定 B)
```
單純只設定第一個條件會使得欄位集合A和B皆為不同的欄位集合，但唯獨欄位集合C無法確定是否源自於A或者B，所以只需要對任意一方設定相同的!->條件就能解決，在這裡額外添加第二個條件就能使欄位集合A、B、C皆為不同的欄位集合。