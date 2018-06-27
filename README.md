## Hello World

Hello World~ This is the blog that I record things.

这是我的个人博客，一路写写停停~

下面是博客搭建教程，更详细教程请戳[如何搭建jekyll主题的github.io博客](http://meilianwu.com/2018/05/31/blog-build.html)

我的[github](https://github.com/wumeilian)

![img](https://raw.githubusercontent.com/wumeilian/wumeilian.github.io/master/img/blog-build-6.png)

## 使用

* 开始
  * [环境](#环境)
  * [修改配置](#修改配置)
  * [撰写博客](#撰写博客)
  
* 组件
  * [侧边栏](#侧边栏)
  * [推荐标签](#featured-tags)
  * [好友链接](#friends)
  * [社交账号](#社交账号)

* 评论

### 环境

本地安装[jekyll](http://jekyllcn.com/)，使用`jekyll serve`或`jekyll serve --watch`命令监听4000端口，打开`http://127.0.0.1:4000/`就可以预览修改内容，需要注意的是，`_config.yml`配置的修改需要重新执行命令。

### 修改配置

`_config.yml`配置文件中添加自己的信息

``` markdown
# Site settings
title: Wml Blog    # blog title
SEOTitle: 吴美莲的博客 | Wml Blog   # seo title
description: "前端开发"  # seo description
url: "https://meilianwu.com/"     # your host, for absolute URL

# Build settings
paginate: 5     # paginate number
plugins: [jekyll-paginate]
```

### 撰写博客

在`_posts`文件下添加新的博客文章，文章可以使用`markdown`编写，也可以使用`textile`格式编写，每篇文章头部都需要添加YAML头信息，它们会将文章内容转换成HTML页面。结构大致是

``` markdown
---
layout:     post
title:      "如何搭建jekyll主题的github.io博客"
subtitle:   ""
author:     "wml"
header-img: "img/blog-build-1.png"
header-mask:  0.5
catalog: true
tags:
    - blog
---
```

### 侧边栏

![sidebar](/img/readme-1.png)

侧边栏的配置在`_config.yml`中

``` markdown
# Sidebar settings
sidebar: true          # 是否使用侧边栏
sidebar-about-description: "Web Developer <br/> 未知的我们向着未知的世界出发"   # 描述自己
sidebar-avatar: /img/avatar.png  # 头像
```

### featured-tags