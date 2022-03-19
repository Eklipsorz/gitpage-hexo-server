---
title: CodeWars Learning - Take a Ten Minute Walk
date: 2021-08-10 22:09:46
tags:
 - JavaScript
categories: CodeWars
---
## Description

You live in the city of Cartesia where all roads are laid out in a perfect grid. You arrived ten minutes too early to an appointment, so you decided to take the opportunity to go for a short walk. The city provides its citizens with a Walk Generating App on their phones -- everytime you press the button it sends you an array of one-letter strings representing directions to walk (eg. ['n', 's', 'w', 'e']). You always walk only a single block for each letter (direction) and you know it takes you one minute to traverse one city block, so create a function that will return true if the walk the app gives you will take you exactly ten minutes (you don't want to be early or late!) and will, of course, return you to your starting point. Return false otherwise.


Note: you will always receive a valid array containing a random assortment of direction letters ('n', 's', 'e', or 'w' only). It will never give you an empty array (that's not a walk, that's standing still!).


## Solution

將東南西北(e、s、w、n)當作x-y座標軸的正負方向，北方和南方是y軸，而東方和西方為x軸，而自己的出發點轉換成(0,0)的座標軸，每當自己往北方走時，y軸的座標就+1；當自己往南方走時，y軸的座標就-1。而自己往東方走時，x軸的座標就+1；反之，自己往西方走時，x軸的座標就-1。當自己走完指定的方向和步數後，其座標還仍為(0,0)就代表著散完步回到出發地。



根據這樣子的規則，我們可以先宣告axis這陣列來定義x-y這兩個座標軸，並且利用陣列內建的foreach以及switch來遍歷自己所走過的方向以及計算當前的座標軸。最後遍歷完之後在檢查看看x-y這兩個座標軸是否皆為0，若為0的話，就是回到出發點或者true，否則就是false。


另外就是題目還指定自己走的步數得是10步，不得少於10步或者多於10步，因此會設定以下的條件式來防止這件事：
```
if (walk.length != 10) {
	return false
} else {
	do something
}
```

整體程式碼為：

```
function isValidWalk(walk) {
  //insert brilliant code here
  
  if (walk.length != 10) {
      return false
  } else {
    let axis = [0, 0]
    
    walk.forEach( element => {
        switch (element) {
            case 'n':
              axis[0]++
              break
            case 's':
              axis[0]--
              break
            case 'e':
              axis[1]++
              break
            case 'w':
              axis[1]--
              break
            }
           
    })

    return axis[0] === 0 && axis[1] === 0
    
  }
  
}
```

另一種解法：

當相對應的方向數都一致時，也可以表明自己已經回到原本的出發地，也就是說自己走了n步向北的方向以及n步向南的方向，這在y軸相當於回到了原點(0,0)中的y軸部分，同樣地若自己走了m步向西的方向以及m向東的方向，在x軸相當於原點(0,0)的x軸部分，兩者情況綜合起來的話，就代表著自己已經回到出發點。所以根據這個方向，我們可以建構一個函式count，該函式會獲取特定符號在字串中出現的數量，接著最後判斷下列兩者情況下是否為true，若true就代表回到出發點了。
```
向北的方向數跟向南的方向數是否一樣？
向西的方向數跟向東的方向數是否一樣？
```

整體的程式碼會是：
```
function isValidWalk(walk) {
  function count(val) {
    return walk.filter(function(a){return a === val;}).length;
  }
  return walk.length==10 && count('n')==count('s') && count('w')==count('e');
}

```

但這種解法的效能會比第一種解法的效能還差，第一種只需要一個for結構就能完成，而第二種解法相當於用了四個for結構來完成，相當於花了4倍的時間去達成目標。
