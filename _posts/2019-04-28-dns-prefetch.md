---
layout:     post
title:      "dns-prefetch"
subtitle:   ""
author:     "wml"
header-img: "img/present/1.jpg"
header-mask:  0.5
catalog: true
tags:

    - html
---
### 背景

在翻阅大厂网站源码的时候，发现head头部有一堆如下图的标签，dns-prefetch作用是什么？使用场景又有哪些？带着这些问题进一步学习。

![1](/img/dns/1.png)

### 概念

dns-prefetch，看名字大概能猜出其作用，dns,域名解析服务，perfetch，预取，所以这个的作用大致就是和域名提前解析有关。

官方的解释是：DNS 预读取是一项使浏览器主动去执行域名解析的功能，其范围包括文档的所有链接，无论是图片的，CSS 的，还是 JavaScript 等其他用户能够点击的 URL。因为预读取会在后台执行，所以 DNS 很可能在链接对应的东西出现之前就已经解析完毕。这能够减少用户点击链接时的延迟。

### 使用

可以看出，dns-prefetch是对域名进行一个提前读取解析的作用，在移动网络环境下，dns的预读取在网络图片多的页面中，在发起请求之前提前把域名解析好，就能有效地提高图片加载速度。

使用方式：

```html
<meta http-equiv="x-dns-prefetch-control" content="on">
```

可以通过meta标签的x-dns-prefetch-control设置dns-prefetch;也可以通过link标签设置

```html
<link rel="dns-prefetch" href="http://static.seeyouyima.com">
```

区别在于link的方式是对特定域名进行预读取，强制查询特定主机名，对网站上频繁使用的域名进行预解析。

### 注意

根据[chrome文档](https://www.chromium.org/developers/design-documents/dns-prefetching)说明

1、chrome会把页面中带`href`的链接如a标签的dns都预解析，但是在`https`的页面中则不会预解析，可以使用上面`<meta>`标签手动开启。

2、dns-prefetch只是域名的提前解析，节省的只是解析时间。

3、网页中使用大量其他域名的资源，则可通过此方式提前解析，同域名下则没啥作用，浏览器在访问网站是就会将dns解析缓存了。