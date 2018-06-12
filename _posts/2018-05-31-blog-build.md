---
layout:     post
title:      "如何搭建jekyll主题的github.io博客"
subtitle:   ""
author:     "wml"
header-img: "img/blog-build-1.png"
header-mask:  0.5
tags:
    - blog
---

## 前言

本文将介绍如何通过github pages 搭建自己的博客，网上相关教程很多，大多教程都是可行，方法也大同小异，接下来，我将详细叙述搭建博客的方法。

## 正文

### 1、 建立属于自己的github.io repository

进入[我的仓库](https://github.com/wumeilian/wumeilian.github.io),点击右上角fork，将我的仓库拉到你自己的账号下; 再点击settings进入设置仓库名 ![fork](/img/blog-build-3.png)

修改仓库名，仓库命名格式必须按照 `<username>.github.io`格式，将`username`替换成你自己的github账户名；![修改仓库名称](/img/blog-build-4.png)

修改完成后，往下拉到Github Pages的位置就能看到修改过后的地址，![pages](/img/blog-build-5.png)

访问该地址就可以看到页面了，如果出现页面打不开，请检查仓库名是否和用户名一致。
![blog](/img/blog-build-6.png)

### 2、拉取项目，更改配置

把fork到自己账户下的项目拉取到本地，可以看到大致文件结构。
![blog](/img/blog-build-7.png)
这里简化了许多目录，其实也是因为暂时没有用上，后续再慢慢添加，对于简单写一写博客应该是足够了；

* `_includes`:  用于存放重用的组件，通过`{` `% include file.ext %` `}`把`_includes/file.ext`文件包含进来；

* `layouts`:  包裹在文章外部的模板，标签`{``{` `comment`  `}``}`可以将内容插入页面；

* `_posts`: 这里就是博客文章了，文件格式要按照`year-month-day-title.MARKUP`的形式，这里貌似`title`也是要使用`xx-xx`的形式；

* `_config.yml`: 这里就是配置文件了，用于配置全局需要的变量或一切其他命令配置。

对于jekyll结构想了解得更详细的可以参看[jekyll教程](https://www.jekyll.com.cn/docs/structure/)。

修改`_config.yml`配置文件

基础配置，可以使用`site.param`引用配置文件内的相关信息;
![config](/img/blog-build-8.png)

侧边栏和添加的第三发社交信息；
![config](/img/blog-build-9.png)  ![config](/img/blog-build-10.png)
可以在`_includes/footer.html`文件下对自己需要展示的信息，根据自己的需求删减。

### 3、添加博客文章

在`_posts`文件下添加新的博客文章，文章可以使用`markdown`编写，也可以使用`textile`格式编写，每篇文章头部都需要添加YAML头信息，它们会将文章内容转换成HTML页面。结构大致是
![YAML](/img/blog-build-11.png)

Jekyll要求文章命名格式遵守`年-月-日-标题.MARKUP`, 例如
> 2011-12-31-new-years-eve-is-awesome.md  
> 2012-09-12-how-to-write-a-blog.textile

### 4、添加分页

当文章数量越来越多时，就需要分页来展示文章列表，jekyll自带分页功能，开启分页功能只需要在`_config.yml`文件中加上
![分页](/img/blog-build-12.png)

### 5、添加域名