---
layout:     post
title:      "浏览器地址栏输入url到页面展示到底发生了什么？"
subtitle:   ""
author:     "wml"
header-img: "img/browser/1.jpg"
header-mask:  0.5
catalog: true
tags:

    - web
---

### 前言

作为一个前端工程师，不可避免的需要了解浏览器工作的原理；

而面试中问得最多的也就是浏览器地址栏输入url到页面展示这个过程发生了什么？

查看相关博客文章，加上自己的理解做一番总结如下：

### 进程与线程

现代操作系统都是支持多任务的，即操作系统可以运行多个任务，比如可以一边浏览器上网，一边听音乐，一边用记事本打字，这就是多任务执行。对操作系统来说，一个任务就是一个进程（process）。进程内又包含一个或者多个线程（thread）协同工作。

进程是cpu资源分配的最小单位，是能有用资源和独立运行的最小单位；

线程是cpu调度的最小单位，是建立成进程基础上的一次程序运行单位。

举个最耳熟能详的工厂例子：

进程是一个工厂，有独立的资源，即操作系统系统分配的独立内存；

工厂相互独立，即进程之间相互独立，可以使用IPC（Inter Process Communication）进行通信；

线程是工厂中的工人，至少有一个或多个工人协同完成工作；

工人共享空间，即线程之间共享内存空间辅助工作。

### 浏览器架构

以Chrome浏览器为例，Chrome采用多进程架构。浏览器每开一个标签页就相当于创建了一个独立的浏览器进程，可通过浏览器自带任务管理器查看进程：

![progress](/img/browser/2.png)

浏览器主要进程及职责如下：

1、Browser Process(浏览器主进程)：

* 负责地址栏，书签栏，前进后退按钮工作；
* 负责各个页面的管理，创建和销毁其他进程；
* 负责浏览器一些不可见的底层操作，如网络资源的管理和下载。

2、Renderer Process（浏览器渲染进程，浏览器内核）：

* 负责一个tab内的页面渲染，脚本执行，事件处理等。

3、Plugin Process（第三方插件进程）：

* 负责控制一个网页中的所有插件，每种类型的插件对应一个进程。

4、GPU Process（GPU进程）

* 负责处理 GPU 相关的任务，最多一个，用于3D绘制。

![progress](/img/browser/3.png)

#### 导航过程发生了啥？

回到问题，在输入框输入文字并回车获取页面内容，浏览器在这个过程中到底发生了什么呢？主要由浏览器主进程和浏览器渲染进程完成工作。

##### 浏览器主进程（Browser Process）

根据上面相关概念，能够知道浏览器Tab外的工作主要都是有Browser Progress即浏览器主进程来处理的，主进程内又包含了不同的线程：

* UI thread: 控制浏览器上的按钮及输入框；
* network thread: 处理网络请求，从网上获取数据；
* storage thead：控制文件等的访问。

主进程主要工作可分一下几步：

1、处理输入

UI线程会判断用户输入的内容，是否是url或者搜索查询，解析并决定更是将内容发送到搜索引擎还是发送到请求的网站。

2、开始导航

用户点击回车后，UI线程通知network thread获取网页内容，并控制tab上的spinner展现，表示正在加载中，network thread会执行DNS查询，为请求建立TLS链接。如果network thread接收到重 定向请求如301，会通知UI线程服务器要求重定向，之后另一个url请求会被触发。

3、读取相应

当请求相应返回时，network线程会根据 Content-Type 及 MIME Type sniffing 判断响应内容的格式，如果是HTML格式的内容，下一步会把这些数据传递给render进程，如果是zip或者其他格式的文件，会把相关数据传输给下载管理器。

4、查找渲染进程

完成检查并且network thread确定浏览器要导航到请求的站点后，network thread会告知UI线程数据准备就绪。UI线程找到render进程进行网页渲染。

由于网络请求需要时间相应，因此应用加速优化方案，UI线程在第二步网络内线发送url请求时，就已经知道导航到哪个站点。但网络线程接受数据时，render进程已经处于备用位置。

5、提交导航

渲染进程准备就绪后，IPC将数据从browser process发送到render process。一旦browser process听到确认提交已在render process中发生，导航就完成了，文档加载阶段就开始了。

![render](/img/browser/4.png)

##### 浏览器渲染进程（Renderer Process）

浏览器内核，即浏览器渲染进程（Renderer Process），页面的渲染，js执行，事件循环等都在这一进程中进行。该进程下有多个线程，主要为：

* 图形用户界面GUI渲染线程：
  * 负责界面渲染，包括解析HTML、CSS、构建DOM树，Render树、布局与绘制等；
  * 界面需要重绘（repaint)或者由于某种操作需要回流（reflow）时，会执行该线程。
* js引擎线程：
  * js内核，负责执行JavaScript脚本；
  * 等待任务队列到来加以处理，浏览器只有一个JS引擎在运行js程序
* 事件触发线程：
  * 事件触发归属浏览器，而不是js引擎，用来控制时间循环
  * js引擎执行setTImeout（或者鼠标点击，ajax异步请求等）会将任务添加到事件线程中；
  * 当对应事件符合触发条件时，该线程会把时间添加到待处理队列的队尾，等js引擎空闲时执行；
  * js引擎是单线程，所以任务队列时间得等待js引擎空闲时处理。
* 定时器触发线程：
  * setInterval与setTimeout所在线程
  * 定时计时器并不是由JS引擎计时的，JS引擎是单线程的，如果JS引擎处于堵塞状态，会影响到计时的准确性
  * 当计时完成被触发，事件会被添加到事件队列，等待JS引擎空闲了执行
* 异步HTTP请求线程
  * 在XMLHttpRequest在连接后新启动的一个线程；
  * 线程如检测到请求状态变更，设有回调函数会把该函数添加到事件队列等待js引擎执行

> * setTimeout--If the currently running task is a task that was created by the setTimeout() method, and timeout is less than 4, then increase timeout to 4. 值得注意的是根据w3c标准，setTimeout中的任务低于4ms会默认为4ms间隔。  
> * setInterval(fn,ms)和setTimeout(fn,ms)不是每过ms秒会执行一次fn,而是每过ms秒会有fn进入事件队列中。

> * 事件触发线程、定时触发器线程、异步HTTP请求线程三个线程有一个共同点，那就是使用回调函数的形式，即异步任务。异步又有宏任务和微任务之分，js事件循环机制为先执行宏任务再执行微任务，在微任务没有执行完前不会进入下一个宏任务。

![render](/img/browser/5.jpg)

##### 浏览器渲染流程

关于浏览器渲染的流程，在之前一篇博客[图片资源加载与渲染](http://meilianwu.com/2018/10/26/img-load.html)中有粗略涉及，这里再赘述一些概念：

* DOM Tree: 常说的DOM树，浏览器将HTML解析成树形的数据结构；
* CSS Rule Tree: css规则树，浏览器将CSS解析成树形的数据结构；
* Render Tree: 渲染树，DOM树和CSS规则树合并后产生的render树；
* layout: 浏览器通过渲染树确定网页中的节点、各节点的CSS定义及从属关系，从而计算出每个节点在屏幕中的位置；
* painting: 绘制，按照计算出来的规则，通过显卡把内容画在屏幕上；
* reflow: 回流，当浏览器发现某部分发生变化影响了布局就需要重新渲染。reflow从\<html>这个根元素开始递归往下计算所有节点的几何尺寸和位置。reflow几乎无法避免，如元素的隐藏和显示，甚至鼠标滑过，点击，只要引起 页面的元素展位面积、定位方式、边距等变化，都会引起内部、周围甚至整体reflow；
* repaint(重绘): 改变元素的背景色，文字、边框颜色等不影响它周围或者内部布局的属性时，屏幕的一部分需要重画，但是几何尺寸没有变化。

> dsplay:none的节点不会被加入Render树中，而visibility:hidden会；所以display:none会触发reflow，visibility:hidden会触发repaint。

浏览器渲染进程，或者说浏览器内核拿到相应的报文后，

1. 解析html生产DOM树；
2. 解析CSS规则；
3. 根据DOM树和CSS规则树生产render树；
4. 根据render树进行layout,计算各个节点的尺寸和位置；
5. painting绘制render树，绘制页面像素信息；
6. 浏览器将各层信息发送给GPU,GPU合成显示在屏幕上。

![render](/img/browser/6.jpg)

上图可以看出，css在加载中不会影响到DOM树的生成，但是会影响render树的身材，从而影响到layout，所以style的link标签尽量放在header里。因为DOM的解析是自上而下的，css样式是通过异步加载的，这样解析DOM树下的节点和加载css样式尽可能并行，加快render树的生成速度。如果css是通过js动态添加的，会引起页面的重绘或者重新布局。

> DOMContentLoaded是仅当DOM加载完成后，不包括样式和图片。eg,异步加载的脚本不一定完成；  
> onLoad事件触发是指页面所有的DOM，样式表，脚本、图片都已经加载完成，渲染完毕后。

### 总结

以上是翻看了许多前辈的博客文章，查阅了相关文档，并做的一些总结，在研究过程中也学习到很多，这其中还有许多细节可以展开讲，前端说到底是一个精益求精的过程，一个合格的前端应该需要多多了解更为本质的东西。工作时间久了，专注于业务开发，很多时候会往往会忽视真正原理性的东西，知其然而不知其所以然；前端领域可以算得上是日新月异了，各种新框架技术层出不穷，但是抽茧剥丝后会发现其实都是基础性的东西。一个好的前端开发工程师，广度固然重要，深度也是必不可少的一环，基础越扎实，才能以不变应万变。

### 相关链接

[What happens in navigation](https://developers.google.com/web/updates/2018/09/inside-browser-part2#what_happens_in_navigation)

[进程与线程的一个简单解释](http://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html)