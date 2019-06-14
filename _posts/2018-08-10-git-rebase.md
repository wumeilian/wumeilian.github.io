---
layout:     post
title:      "git rebase"
subtitle:   ""
author:     "wml"
header-img: "img/bg-1.png"
header-mask:  0.5
catalog: true
tags:

    - web
---

### 背景

使用git开发过程中，尤其是团队开发时，合并操作用的较多的，大概都是`git merge`,关于git merge这里不做详述，相信在在使用过程中，无论是使用git命令或者图形化操作软件，如Smartgit或者Sourcetree。开发中经常可以看到git的历史树是这样的：

![eg](/img/rebase/2.png)

交杂错乱，对于后续走查和交接来说简直苦不堪言，
导致这样的问题，大多数是因为我们使用merge直接去合并分支；
所以主干上就不断出现`Merge commit xxx into xxx`酱紫的干扰信息。一个干净漂亮的历史树对于规范的git使用来说是必不可少的，Git中有一种成为rebase的操作就可以解决，又被成为“变基”。

### Rebase 原理

rebase的原理是合并时把你本地的提交（commit）取消，临时保存为补丁（patch）
并放在`.git/rebase`目录中；本地更新为远程最新以后，再把补丁应用到本地分支中。它和merge的区别主要是：

![eg](/img/rebase/3.png)
![eg](/img/rebase/4.png)

### Rebase 使用

如何在git中使用rebase呢，举个栗子~

![eg](/img/rebase/5.png)

从历史树上可以看到，我在未更新远程分支的前提下，提交了一个`打印log”的记录`；正常使用merge的话，就会产生一个如上图那样叉开的树；但如果使用rebase去合并的话；具体操作如下图：

![eg](/img/rebase/6.png)

合并后的历史树，以及哈希值

![eg](/img/rebase/7.png)

合并前的该提交的哈希值

![eg](/img/rebase/8.png)

在rebase时，解决完冲突之后，git add 之后不需要提交（commit）,使用`git rebase --continue`就会
apply余下的补丁；或者也可以使用`git rebase --abort`来终止rebase，回到之前的状态。

### 总结

需要注意的是，rebase会让你的历史树变得干净整洁，但同时，rebase会改写记录，如前图，c5',c6'和c5,c6提交的内容虽然一样，但确实两个不同的提交，所以hash值也会变更。rebase的使用需要小心谨慎，如果使用不当，就会引起更大的问题。之前有遇到过在自己分支合并到release时，发现自己分支上存在大量别人的代码，并每次提交都有冲突，很大的可能就是因为误操作到了rebase, 当把release rebase to到开发分支时，会产生新的提交，git会认为你的release和远程的release分叉了，所以就冲突不断，如果这时候merge到一起，就会产生额外的相同内容的提交。

![eg](/img/rebase/9.png)

> Rebase 的黄金法则：绝不要在公共的分支上使用它。理解rebase的同时，更需要知道什么时候不能用rebase。
