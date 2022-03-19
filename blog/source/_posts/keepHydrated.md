---
title: CodeWars Learning - Keep Hydrated!
date: 2021-08-10 21:34:49
tags:
 - JavaScript
categories: CodeWars
---
## Description

Nathan loves cycling.

Because Nathan knows it is important to stay hydrated, he drinks 0.5 litres of water per hour of cycling.

You get given the time in hours and you need to return the number of litres Nathan will drink, rounded to the smallest value.

For example:


```
time = 3 ----> litres = 1

time = 6.7---> litres = 3

time = 11.8--> litres = 5
```

## Solution

透過每小時喝的水量來進行換算就能得到需要喝的水量，最後由於題目需要無條件捨去，所以得呼叫Math物件的floor方法來實現。

```
function litres(time) {
  return Math.floor(time*0.5)
}
```

