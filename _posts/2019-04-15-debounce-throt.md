---
layout:     post
title:      "防抖和节流"
subtitle:   ""
author:     "wml"
header-img: "img/present/1.jpg"
header-mask:  0.5
catalog: true
tags:

    - js
---

### 背景

防抖和节流在面试过程中应该被问的频率蛮高了，工作中也时常遇到，在浏览器中，当频繁操作某个函数，且响应速度比触发速度低时，会出现卡顿，延迟，甚至假死状态，比如根据用户输入查询接口，滚动加载等；为了提高性能，不造成资源浪费，使用防抖和节流防止函数被高频触发。

### 防抖debounce

事件被触发时，设置定时器，延迟n秒后执行，如果n秒内该事件被再次触发，则重新计时，将频繁的事件合并为最后一次执行。

看到的一个例子觉得很形象: 电梯5秒后关门运作，有人进来，等待5秒后关门，如果期间有人进来，则又开始重新等待5秒，直到计时超过5秒，电梯关门运作。

```html
<input type="text" id="debounce"/>

<script>
  function ajaxApi(data) { // 模拟数据请求
    console.log(new Date + ' - ' + data);
  }

  function debounce(fn, wait, immediate) {
    let timeout;
    return function() {
      let contxt = this;
      if(timeout) clearTimeout(timeout);

      if(immediate) {
          let callNow = !timeout;
          timeout = setTimeout(() => {
              timeout = null;
          }, wait);
          callNow && fn.call(contxt, ...arguments);
      }
      else {
          timeout = setTimeout(() => {
              fn.call(contxt, ...arguments);
          }, wait);
      }
    } 
}
  // var debouceAjax = debounce(ajaxApi, 1000); //高阶函数
  // document.getElementById('debounce').addEventListener('keyup', e => {
  //   debouceAjax(e.target.value)
  // })
  var debouceAjax = debounce(e => ajaxApi(e), 1000); 
  document.getElementById('debounce').addEventListener('keyup', debouceAjax);
</script>
```

### 节流throttle

事件在被触发的n秒内，即使多次被触发也只执行一次。

```html
<input type="text" id="throttle"/>

<script>
    function ajaxApi(data) { // 模拟数据请求
        console.log(new Date + ' - ' + data);
    }

    function throttle(fn, time) {
        let timeout = null;  // 这里可以指定为一个定时器，或者直接加一个变量标识也行，原理都是一样
        return () => {
            if(timeout) return;  //如果存在计时则等待计时结束
            timeout = setTimeout(() => {
                  fn.call(this, ...arguments);
                  clearTimeout(timeout);  // 回收定时器
                  timeout = null;
            }, time)
        }
    }

    var throttleAjax = throttle(ajaxApi, 1000);

    document.getElementById('debounce').addEventListener('keyup', e => {
        throttleAjax(e.target.value)
    })
</script>
```

防抖和节流很相似，区别在于，防抖n秒内事件只要再次被触发就重新计时，但是节流则是n秒内只执行一次。

使用场景上，防抖适用于输入数据请求服务端这种情况；节流更适用于页面滚动或者上拉加载更多的场景。
