---
layout:     post
title:      "[今日算法] 经典的数据结构实现（js版）"
subtitle:   ""
author:     "wml"
header-img: "img/algorithm/bg.jpg"
header-mask:  0.5
catalog: true
tags:
    - 算法
---

#### 1、斐波那契数

又称“黄金分割数列”，从第三项开始，每一项等于前两项的和，自然界中如向日葵花瓣就是斐波那契数列。

```js
// 0 1 1 2 3 5 8 ... 求第n项的数
// 递归实现,时间复杂度O(2^n)
function fibonacci(n) {
  if(n < 2) return n;
  return fibonacci(n-1) + fibonacci(n-2);
}

// 非递归实现， 复杂度O(n)
function fib2(n) {
  if(n < 2) return n;
  let fir = 0;
  let sec = 1;
  for(let i = 0; i < n-1; i++) {
  // for(let i = 2; i <= n; i++) {
    let sum = fir + sec;
    fir = sec;
    sec = sum;
  }
  return sec;
}

// 结果
console.time('time');
console.log(fib2(32));
console.timeEnd('time')
// 2178309
// time: 0.0859375ms

console.time('time');
console.log(fib2(64));
console.timeEnd('time')
// 10610209857723
// time: 0.14990234375ms

console.time('time1');
console.log(fibonacci(32));
console.timeEnd('time1')
// 2178309
// time1: 31.411865234375ms

console.time('time1');
console.log(fibonacci(64));
console.timeEnd('time1')
// 浏览器崩了==
```

#### 2、约瑟夫问题

N个人围成一圈，从第一个开始报数，第M个将被杀掉，直到全部被杀光；可以用环形链表的方式解决该问题。

```js

```

#### 3、波兰式和逆波兰式

