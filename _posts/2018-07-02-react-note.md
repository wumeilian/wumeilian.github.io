---
layout:     post
title:      "React的那些事儿"
subtitle:   ""
author:     "wml"
header-img: "img/home-bg.jpg"
header-mask:  0.5
catalog: true
tags:

    - 前端
    - react
---

## 前言

React是Facebook开源的一个用于构建用户界面的Javascript库，已经应用于Facebook及旗下Instagram。和庞大的AngularJS不同，React专注于MVC架构中的V，即视图。

虚拟DOM和组件化是react的两大特点。虚拟DOM是React的基石。它的两大优点在于：一是性能方面，web页面中会有大量的DOM操作使得页面渲染速度变慢，React优化了这个问题，在每次渲染时会比对差异，然后再最优地更新DOM；引入虚拟DOM的另一个好处是，容易引入不同的渲染引擎。比如将你的应用代码渲染 到真实的DOM，或者nodejs服务端的无头DOM，或者，iOS/Android平台组件 —— 这就是 React Native ：

### React组件

  react的一个最大的特点就是组件化，将页面像搭积木一样由组件一块块拼装起来；

### 关于jsx

* jsx是什么：

    像xml的JavaScript语法拓展，在React不是必须的。

* 为什么要使用jsx语法：

    1、避免建造大量的dom树，编写模板更加简单快速；

    2、执行更快，在编译为JavaScript后进行了优化；

    3、类型安全，编译过程中就能发现错误

* 使用jsx

    jsx使用看起来类似HTML，经过babel转换成react.createElement()形式;

```javascript
/*jsx*/
var ele = (
   <div className="title">
    <div className="txt">Hello, React!</div>
  </div>
)

/*babel转换后*/
var ele = React.createElement(
    "div",{className: "title"},
    React.createElement("div",{className:"txt"},"Hello, React!"),
);
```