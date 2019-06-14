---
layout:     post
title:      "[今日算法] 两数相加"
subtitle:   ""
author:     "wml"
header-img: "img/algorithm/bg.jpg"
header-mask:  0.5
catalog: true
tags:

    - 算法
---

#### 题目描述

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例 1:

> 输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
> 输出：7 -> 0 -> 8
> 原因：342 + 465 = 807

#### 难度

中等

#### 解答

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var addTwoNumbers = function(l1, l2) {
    var curA = l1;
    var curB = l2;
    var resultNode = new ListNode(0);
    var newNode = resultNode;
    var carry = 0; //进位

    while(curA != null || curB != null) {
        // 兼容长度不一致的情况
        var curAval = curA != null ? curA.val : 0;
        var curBval = curB != null ? curB.val : 0;

        var sum = curAval + curBval + carry;

        var num = sum % 10;  // 当前位
        carry = Math.floor(sum/10); // 进位
        // carry = sum > 9 ? 1 : 0;

        newNode.next = new ListNode(num);

        newNode = newNode.next;

        curA = curA != null ? curA.next : null;
        curB = curB != null ? curB.next : null;
    }

    if(carry > 0) {
        newNode.next = new ListNode(carry);
    }

    return resultNode.next;
};
```

提交结果

![1](/img/algorithm/day3-1.png)

#### 分析
