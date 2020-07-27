---
layout:     post
title:      "团队开发时应该要遵守的git提交规范"
subtitle:   ""
author:     "wml"
header-img: "img/bg-1.png"
header-mask:  0.5
catalog: true
tags:
    - web
---

## 背景

实际开发中，项目往往是多人开发的情况更多；在翻看提交内容查找关键信息过程中，毫无重点的提交信息大家看着有多心累就不用多说了吧；所以遵守一个公共且易懂的提交规范对于开发工作中追本溯源是非常重要的辅助方法。现在比较受大众认可的是`约定式提交规范`，它是受Angular提交准则启发，是一种轻量级的约定，简单易上手；以下就是简要描述记录下该规范内容：


## git commit 建议格式

commit message 包括三部分， header(必须)、body(可选)、footer(可选)；

### Header:

只有一行，包括三个字段： type(必须)、 scrope(可选)、subject(必须)

#### --type: 

  说明commit的类别，只允许使用以下7个标识：

  * feat: 新功能(feature)
  * fix: 修补bug
  * docs: 文档(documentation)
  * style: 格式(不影响代码运行的变动)
  * refactor: 重构(即不是新增功能，也不是修改bug的代码变动)
  * test: 增加测试
  * chore: 构建过程或者辅助工具的变动

#### --scope：

  说明commit影响的范围，如数据层、控制层、视图层等，也可按页面改动变更，
  若修改不止一个，可用*代替

#### --subject

  commit目的的简短描述，不超过50个字符

### Body： 

  对commit的详细描述，可分行；

### Footer: 

  只适用于不兼容变动和关闭issure的情况。

### Example:

feat(store): 增加layerAuth数据

fix(views): 修复点位分析权限码展示问题

docs(picker): 补充picker插件文档说明

style(views): 代码缩进/样式修改

refactor(picker): 重构picker组件

test(picker): 日志输出

chore(wepack): 增加webpack文件分析插件

## 番外

关于git提交过程中遇到的一些问题，记录一哈

### 解决git bash提交更新代码每次都要输入用户名密码

由于更改过密码，导致每次提交代码都需要手动输入用户名密码，极大地影响效率和体验，解决办法如下：

1、git bash进入对应项目目录，输入命令 `git config --global credential.helper store`
该命令会在本地生成一个.git-credentials的文件`(c:/Users/userName)`;

2、更新下你的项目，eg `git push`,按提示输入用户名和密码，此时会在`.git-credentials`的文件中生成形如
`https:{username}:{password}@github.com`格式的你的用户信息，并且在`~/.gitconfig`文件末尾添加如下配置:

```
[credential]
	helper = store
```

3、再次更新项目就会发现不需要再次输入用户密码了

### git bash密码更新

git config --global user.name "wml(新的用户名)"

git config --global user.email "wml@qq.com(新的邮箱)"

git config --global user.password "123456(新的密码)