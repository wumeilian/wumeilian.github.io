---
layout:     post
title:      "[今日算法] js版常见数据结构类型实现"
subtitle:   ""
author:     "wml"
header-img: "img/algorithm/bg.jpg"
header-mask:  0.5
catalog: true
tags:
    - 算法
---

#### 1、斐波那契数

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

