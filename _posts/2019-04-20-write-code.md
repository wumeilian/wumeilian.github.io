---
layout:     post
title:      "搞定前端手写"
subtitle:   ""
author:     "wml"
header-img: "img/bg-1.png"
header-mask:  0.5
catalog: true
tags:
    - js
---

#### 1、手写call

call()接受的是一个参数列表

```js
let obj = {name: 'wml'};
function foo(age, sex) {
  return {
    name: this.name,
    age,
    sex
  }
}

Function.prototype.myCall = function() {
  const cxt = arguments[0] || window; // 若没有传入this, 默认绑定window对象
  const args = [...arguments].slice(1);  // es6获取剩余参数
  // 也可用symbol防止fn被覆盖
  // const fn = Symbol('fn');
  // cxt[fn] = this;
  cxt.fn = this; //this指向调用call的对象,即我们要改变this指向的函数
  const result = cxt.fn(...args); // 执行当前函数
  delete cxt.fn; // 删除声明的fn属性
  return result;
}

foo.myCall(obj, 18, 'girl'); // {name: "wml", age: 18, sex: "girl"}
```

#### 2、手写apply

apply()第二个参数接受的是一个包含多个参数的数组

```js
Function.prototype.myApply = function() {
  const cxt = arguments[0] || window; // 若没有传入this, 默认绑定window对象
  const args = arguments[1] || [];
  // 也可用symbol防止fn被覆盖
  // const fn = Symbol('fn');
  // cxt[fn] = this;
  cxt.fn = this; //this指向调用call的对象,即我们要改变this指向的函数
  const result = cxt.fn(...args); // 执行当前函数
  delete cxt.fn; // 删除声明的fn属性
  return result;
}

foo.myApply(obj, [18, 'girl']); // {name: "wml", age: 18, sex: "girl"}
```

#### 3、手写bind

传参与call一样，但返回的是一个函数

```js
Function.prototype.myBind = function() {
  const cxt = arguments[0] || windows;
  const args = [...arguments].slice(1);
  cxt.fn = this;
  const result = function() {
    const args2 = [...arguments];
    return cxt.fn(...args, ...args2);
    // return cxt.fn.apply(cxt, [...args, ...args2]);
  }
  return reslut;
}
foo.myBind(obj, 18, 'girl')(); // {name: "wml", age: 18, sex: "girl"}
foo.myBind(obj)(18, 'girl'); // {name: "wml", age: 18, sex: "girl"}
```


#### 4、实现new

```js
function news(P, ...rest) {
  let obj = {};
  obj.__proto__ = p.prototype;
  P.apply(obj, rest);
  return obj;
}
let Person = function(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
};
let p1 = myNew(Person,"Ysir",24,"stu");
p1.__proto__ === Person.prototype // true
```

#### 4、实现instanceof

```js
function myInstanceOf(obj, cons) {
  if(typeof cons !== 'function') throw new Error('instance error');
  if(!obj || (typeof obj !== 'object' && typeof obj !== 'function')) return false;
  let proto = cons.prototype;
  while(obj.__proto__) {
    if(obj.__proto__ === proto) return true;
    obj = obj.__proto__;
  }
  return false;
}
myInstanceOf(p1, Person); // true
```

#### 5、手写$on, $once, $off, $emit

详见[vue.js的事件机制实现](http://meilianwu.com/2019/03/26/vue-event.html)