---
layout:     post
title:      "如何搭建jekyll主题的github.io博客"
subtitle:   ""
author:     "wml"
header-img: "img/bg-1.png"
header-mask:  0.5
catalog: true
tags:
    - 笔记
---

## 前言

本文将介绍如何通过github pages 搭建自己的博客，网上相关教程很多，大多教程都是可行，方法也大同小异，接下来，我将详细叙述搭建博客的方法。

## 正文

### 1、 建立属于自己的github.io repository

进入[我的仓库](https://github.com/wumeilian/wumeilian.github.io),点击右上角fork，将我的仓库拉到你自己的账号下; 再点击settings进入设置仓库名 ![fork](/img/blog/blog-build-3.png)

修改仓库名，仓库命名格式必须按照 `<username>.github.io`格式，将`username`替换成你自己的github账户名；![修改仓库名称](/img/blog/blog-build-4.png)

修改完成后，往下拉到Github Pages的位置就能看到修改过后的地址(这里设置了自己的域名，未设置时地址`http://wumeilian.github.io`)
![pages](/img/blog/blog-build-5.png)

访问该地址就可以看到页面了，如果出现页面打不开，请检查仓库名是否和用户名一致。
![blog](/img/blog/blog-build-6.png)

### 2、拉取项目，更改配置

把fork到自己账户下的项目拉取到本地，可以看到大致文件结构。
![blog](/img/blog/blog-build-7.png)
这里简化了许多目录，其实也是因为暂时没有用上，后续再慢慢添加，对于简单写一写博客应该是足够了；

* `_includes`:  用于存放重用的组件，通过`{` `% include file.ext %` `}`把`_includes/file.ext`文件包含进来；

* `layouts`:  包裹在文章外部的模板，标签`{``{` `comment`  `}``}`可以将内容插入页面；

* `_posts`: 这里就是博客文章了，文件格式要按照`year-month-day-title.MARKUP`的形式，这里貌似`title`也是要使用`xx-xx`的形式；

* `_config.yml`: 这里就是配置文件了，用于配置全局需要的变量或一切其他命令配置。

对于jekyll结构想了解得更详细的可以参看[jekyll教程](https://www.jekyll.com.cn/docs/structure/)。

修改`_config.yml`配置文件

基础配置，可以使用`site.param`引用配置文件内的相关信息;
![config](/img/blog/blog-build-8.png)

侧边栏和添加的第三发社交信息；
![config](/img/blog/blog-build-9.png)  ![config](/img/blog/blog-build-10.png)
可以在`_includes/footer.html`文件下对自己需要展示的信息，根据自己的需求删减。

### 3、添加博客文章

在`_posts`文件下添加新的博客文章，文章可以使用`markdown`编写，也可以使用`textile`格式编写，每篇文章头部都需要添加YAML头信息，它们会将文章内容转换成HTML页面。结构大致是
![YAML](/img/blog/blog-build-11.png)

Jekyll要求文章命名格式遵守`年-月-日-标题.MARKUP`, 例如
> 2011-12-31-new-years-eve-is-awesome.md  
> 2012-09-12-how-to-write-a-blog.textile

### 4、添加分页

当文章数量越来越多时，就需要分页来展示文章列表，jekyll自带分页功能，开启分页功能只需要在`_config.yml`文件中加上
![分页](/img/blog/blog-build-12.png)

### 5、添加域名

Github Pages 可以更换自己的域名，关于域名购买以及解析，我是在阿里云上购买的域名，网上也很多相关教程，这里不再赘述；如果想换成自己的域名也很简单，只需要在根目录下添加`CNAME`文件，并且添加自己的域名即可。
![域名](/img/blog/blog-build-13.png)

### 6、本地调试

至此，博客基本是搭建好了。但是每次更新都要提交到远程仓库里才能看到结果，对于时常需要更新博客的同学来说可以说很不方便了；所幸，jekyll实现了本地调试。虽然官方不建议在Windows平台上安装Jekyll，emmmm...毕竟Windows用户还是很多的。。下面介绍在Windows下安装jekyll(os和Unix/Linux 用户安装比较简单，自行查阅吧。。。);

由于jekyll是用ruby语言实现的静态网页生成工具，所以要搭建jekyll环境就需要先配置好ruby环境；

* 下载[ruby](https://www.ruby-lang.org/zh_cn/downloads/)，使用RubyInstaller安装，wins下需要添加DevKit，所以选择下面的版本；

![ruby](/img/blog/blog-build-14.png)

* 下载完以后按引导安装，这里需要勾选安装MSYS2，用于shell命令开发环境下的windows软件；

![ruby](/img/blog/blog-build-15.png)

* 安装完ruby环境后，`gem install jekyll` 安装jekyll，查看是否安装成功

![ruby](/img/blog/blog-build-16.png)

* 启动jekyll，在命令行内输入 `jekyll serve --watch` 服务，默认端口号4000，就可以通过`http://localhost:4000`访问本地博客啦。

### 7、添加gitalk评论插件

![gitalk](/img/blog/blog-build-17.png)

这里使用的是Gitalk评论插件，基于github issue和preact开发的插件，网上也有很多使用Disqus评论插件，不过由于国内网络支持问题，Disqus可能加载不出来。关于添加gitalk插件的方法很简单，将下面这段代码插入`_layouts/_post.html`中

```html
<!-- Gitalk 评论 start  -->
<!-- Link Gitalk 的支持文件  -->
<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
<script src="https://unpkg.com/gitalk@latest/dist/gitalk.min.js"></script> 
<div id="gitalk-container"></div>
<script type="text/javascript">
    var gitalk = new Gitalk({
    // gitalk的主要参数
    clientID: `Github Application clientID`,
    clientSecret: `Github Application clientSecret`,
    repo: `存储你评论 issue 的 Github 仓库名`,
    owner: 'Github 用户名',
    admin: ['Github 用户名'],
    id: '页面的唯一标识，gitalk会根据这个标识自动创建的issue的标签',
    });
    gitalk.render('gitalk-container');
</script>
<!-- Gitalk end -->
```

创建一个[github application](https://github.com/settings/applications/new)；

![blog](/img/blog/blog-build-18.png)

创建好产生的Client ID 和 Client Secret 填入你的我们 Gitalk 参数中，添加完成后需要push到远程仓库中，每篇文章会产生一个对应的issue。需要注意的是，首次加载需要在仓库中创建对应issue所以速度会慢一些，初始化后需要授权，按提示点击授权即可。

## 写在最后

关于github博客的搭建，由于各文件是后续一点点加上去的，所以还存在着许多不足，比如，对于图片的压缩，以及文件的自动压缩，还有博客评论等，都还没有添加，前期简单记录是足够了，还需要后面慢慢润色，最后，如果本文有帮助到你的话，进入[我的仓库](https://github.com/wumeilian/wumeilian.github.io)帮忙点个star吧~比熏熏٩(๑❛ᴗ❛๑)۶。