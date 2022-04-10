---
title: mergesort
tags:
 - sorting
categories:
 - algorithm
---

## 背景
合併排序(merge sorting) 是一種排序法，將一組存放多個數字的陣列A按照數字大小來轉換成從小至到大的陣列A'或者從大至小的陣列A'，排序原理則是依照 **將陣列A拆分成已經排序好的陣列B、C，並依序分別從陣列B和陣列C挑出一個元素來比較，最小的元素則放入新陣列D，接著下一次從陣列B、C還未放入陣列D的元素來比較並插入，從中構成合併的動作**，比如說

放例子圖



，然而一開始實際上並沒有**所謂已經排序好的陣列B、C**，但可以透過divide and conquer方法中的分割(divide)將陣列A
切割成好幾等份的子陣列，每一個子陣列都只有一個元素，這樣就能構成已經排序好的陣列，在這樣情況下，就從中挑出兩個較為鄰近的子陣列來比較合併，來構成擁有兩個已經排序好的元素之陣列，

// 放例子圖

現在每個子陣列是兩個已經排序好的元素之陣列，接著再從兩個較為鄰近的子陣列比較合併，一直到最一開始的情景 **將陣列A拆分成已經排序好的陣列B、C** 來比較合併就能將陣列A轉換成排序好順序的陣列A'，而已經排序好的陣列B、C就會是原本陣列A的前半段和後半段，


切割至只剩下一個元素的子陣列B和一個元素的子陣列C，這樣只剩下一個元素的情況下，就是已經排序好的陣列B、C，接著只要從中比較元素放入新陣列就可以構成下一次要被比較合併的已排序之陣列，




2. 

## 基本操作
合併 (merge)：將兩個已經排序好的陣列合併成一個陣列，合併後的結果即為目前結果

比較次數最多為N-1次，原因在於每一次比較皆會把比較結果放入至暫時陣列，若要考慮最多的比較次數，則只需要確認是否會按照順序從分割陣列中拿出元素放入暫時陣列，結果是可以肯定的，所以除了最後一個元素不需要比較以外，那麼就只要算上分割陣列的所有元素總數N，接著扣掉不需要比較的次數1，換言之就是N-1

### 合併


### 分割


### C語言為例：原始碼

```
void MSort(ElementType A[], ElementType TmpArray[], int Left, int Right) {
  int Center;
  if (Left < Right) {
    Center = (Left + Right) / 2;
    MSort(A, TmpArray, Left, Center);
    MSort(A, TmpArray, Center + 1, Right);
    Merge(A, TmpArray, Left, Center + 1, Right);
  }
}

void Mergesort(ElementType A[], int N) {
  ElementType *TmpArray; 
  TmpArray = malloc(N * sizeof(ElementType));
  
  if (TmpArray != NULL) {
    MSort(A, TmpArray, 0, N - 1);
    free(TmpArray);
  }
  else
    FatalError("No Space");
}
```

 TmpPos = Lpos 會採用Lpos 是因為剛好會是tmpArray能放的初始位置
```
void Merge(ElementType A[], ElementType TmpArray[], int Lpos, int Rpos, int RightEnd) {
  int i, LeftEnd, NumElements, TmpPos;

  Left = Rpos - 1;
  TmpPos = Lpos;
  NumElements = RightEnd - Lpos + 1;

  while (Lpos <= LeftEnd && Rpost <= RightEnd) {
    if (A[Lpos] <= A[Rpos])
      TmpArray[TmpPos++] = A[Lpos++];
    else
      TmpArray[TmpPos++] = A[Rpos++];
  }

  while (Lpos <= LeftEnd) {
    TmpArray[TmpPos++] = A[Lpos++];
  }
  while (Rpos <= RightEnd) {
    TmpArray[TmpPos++] = A[Rpos++];
  }

  for (i = 0; i < NumElements; i++, RightEnd--) {
    A[RightEnd] = TmpArray[RightEnd];
  }
}
```
為啥是RightEnd？ 首先i和NumElements是按照可放的元素數，
```
A[RightEnd] = TmpArray[RightEnd];
```
### JavaScript：實現代碼

## 前置知識：Divide and Conquer
當面對的問題無法一時之間解決或者過於龐大時，可以試著使用Divide and Conquer策略來設法解決，其概念有三個核心要點：
  - 分解 (Divide)： 將原問題分解為數個規模較小、獨立、與原問題形式相同的子問題，對於原問題而言，這些子問題結合起來就構成其原本問題
  > Divide the problem into a number of subproblems that are smaller instances of the same problem.
  - 解決 (Conquer)： 通過遞迴式的分割來解決子問題，若切割至規模小到可以輕易解決時(如數個可能解只剩下只剩下一個)，就直接以回傳該解來解決
  > Conquer the subproblems by solving them recursively. If they are small enough, solve the subproblems as base cases.
  - 合併 (Combine)： 將各子問題的解合併為原問題(切割前的問題)的解
  > Combine the solutions to the subproblems into the solution for the original problem.

舉一個例子，假設有個原始問題Problem過於龐大並不能直接解答，因此會將問題切割(Divide) **問題形式相同、獨立、較小的子問題Sub-Problem**，在這裡為了很好分辨哪個才是子問題以及原問題，所以使用Level來區分，Level 0 為最原始的問題，Level 1為Level 0問題所切割下來的子問題，但由於Level 1的子問題還是過於龐大，因而繼續切割成子問題而構成Level 2的子問題，這時的子問題已經輕易解決(Conquer)就直接回傳其解，Level 2的子問題就因而被解決，接著利用 **原問題是由子問題所構成的特性** 來將Level 2獲取到的解結合(Combine)成Level 1子問題的解，這時Level 1的子問題就因而被解決，最後再再將Level 1的解合併成Level 0問題的解，就能解決最原始的問題。
![](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1649582665/blog/algorithm/divide-and-conquer-progression_nzvkch.png)


參考資料：
[Divide-and-conquer](https://www.khanacademy.org/computing/computer-science/algorithms/merge-sort/a/divide-and-conquer-algorithms)