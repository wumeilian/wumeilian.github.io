---
layout:     post
title:      "关于es6类里的super方法和get set方法"
subtitle:   ""
author:     "wml"
header-img: "img/present/1.jpg"
header-mask:  0.5
catalog: true
tags:

    - js
---

#### es6 Class

> * 类继承父类时，super()方法只能在构造函数中使用，且必须在使用this关键字之前调用
> * class内部使用的set get关键字是用来对`某个属性`设置存值函数和取值函数

```js
class Esc {
  constructor(id, region, name) {
    this.id = id;
    this.region = region;
    this.name = name;
  }
  buy() {
    return `https://xxxx.com?id=${this.id}&region=${this.region}&name=${this.name}`;
  }
}

class SubEsc extends Esc {
  constructor(id, region, name) {
    // Uncaught ReferenceError:Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    //this.instance = name;  

    super(id, region, name);

    this.instance = name;  
  }

  get config() {
    return this
  }

  set props(value) {
    console.log(value, 333)
  }
}

let subesc = new SubEsc(1, 'xm', 'wml')
```

> * 每个实例对象（object）都有一个私有属性（__proto__）指向它的原型对象(prototype)，层层向上，直到一个对象的原型对象为null,null没有原型，被称为原型链的最后一环。下图可以看出上面subesc实例的原型链是`{id: 1; instance: "wml";name: "wml";region: "xm"}  --> SubEsc.prototype --> Esc.prototype --> Object.prototype --> null`.

![present](/img/present/2.jpg)