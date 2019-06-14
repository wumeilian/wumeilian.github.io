---
layout:     post
title:      "flex布局属性总结"
subtitle:   ""
author:     "wml"
header-img: "img/present/1.jpg"
header-mask:  0.5
catalog: true
tags:

    - css
---

#### flex布局

flex布局使用次数很多了，不过对于其属性值，每次都是用到了临时查阅文档，没有真正系统的总结过。更谈不上对答如流了。

`display: flex | inline-flex`

flex弹性布局，父元素为容器，有水平的主轴和垂直的交叉轴，子元素为项目；

容器的属性：  
flex-direction: row | row-reverse | column | column-reverse (主轴排列方向，即项目排列方向)

flex-wrap: nowrap \| wrap \| wrap-reverse (多条轴线排布)

flex-flow: \<flex-direction> || \<flex-wrap> (两属性简写，默认为row nowrap)

justify-content: flex-start \| flex-end \| center \| space-between \| space-around; (项目在主轴上的对齐方式)

align-items: flex-start \| flex-end \| center \| baseline \| strentch; (项目在交叉轴的对齐方式)

align-content: flex-start \| flex-end \| center \| space-between \| space-around \| stretch (多根轴线的(垂直方向的)对齐方式， 一根轴线不起作用)

项目的属性：  
order: 0 (项目的排列顺序，数字越小越靠前)

flex-grow: 0 (有剩余空间，项目的放大比例，默认不放大)

flex-shrink: 1 (空间不足，项目的缩小比例，默认缩小，为0不缩小)

flex-basis: \<length> \| auto; (项目在分配空间前的基准值，主轴横向表宽，主轴纵向表高)

flex: none \| \[ \<'flex-grow'> \<'flex-shrink'>? \|\| \<'flex-basis'> ] (这三个值的缩写， 默认 0 1 auto 后两个可选，快捷值auto[1 1 auto] 和none[0 0 auto]

align-self: auto \| flex-start \| flex-end \| center \| baseline \stretch; (允许单个项目有不一样的对齐方式(垂直方向)，可覆盖align-items,默认auto)
