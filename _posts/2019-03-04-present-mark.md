---
layout:     post
title:      "今日份小记"
subtitle:   ""
author:     "wml"
header-img: "img/present/1.jpg"
header-mask:  0.5
catalog: true
tags:

    - 小记
    - js
    - web
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

#### 网络分层模型（OSI）(3/4)

`OSI(open systems interconncecion)`模型,开放系统互联，
分为七层：
`application(应用层)`、`presentation(表达层)`、`session(会话层)`
`transport(传输层)`、`data link(交换机)`、`physical(物理层)`

速记：`all people seem to need data processing`

TCP/IP协议是传输层协议，主要解决数据如何在网络中传输，而HTTP是应用层协议，主要解决如何包装数据

#### vue展示换行和空白符(3/1)

文本中含有换行符空格等，在vue内不换行，而换行符显示为一个空格，
解决办法有两种：

1、用v-html,将文本内的'\r\n'替换为`<br>`再显示，
但该方法首先不同系统的换行符不同，处理比较麻烦，同时会有安全问题，xss攻击

2、css方法：（推荐）
设置white-space: pre-wrap, 该值表示保留空白并正常换行，
white-space取值详见[white-space](http://www.w3school.com.cn/cssref/pr_text_white-space.asp)

#### qq二次分享 (2/28)

title设置（title动态生成），qq浏览器在二次分享时，在安卓设备下
不显示，使用

```html
<meta itemprop="name" content="xxx"/>解决
<meta itemprop="name" content="分享标题"/>
<meta itemprop="image" content="http://img.xmiles.cn/fortune/icon.png" />
<meta name="description" itemprop="description" content="分享描述" />
```

#### CLI之package.json (2/20)

cli命令不能用.

error:

```json
 "bin": {
    "meetyou.alioss": "index.js"
  },
```

success:

```json
 "bin": {
    "meetyou-alioss": "index.js"
  },
```