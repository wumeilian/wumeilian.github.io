---
layout:     post
title:      "[今日算法] 两数之和"
subtitle:   ""
author:     "wml"
header-img: "img/algorithm/bg.jpg"
header-mask:  0.5
catalog: true
tags:

    - 算法
---
## 背景

作为前端开发，对算法的要求可能没有那么高，但还是要有一些了解和涉猎 。新发现一个算法的网站[力扣](https://leetcode-cn.com/problemset/all/)，里面有大量从易到难的算法题，每日一练，相信时间久了肯定大有收获。

#### 难度

简单

#### 题目描述

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:

> 给定 nums = [2, 7, 11, 15], target = 9
>
> 因为 nums[0] + nums[1] = 2 + 7 = 9
> 所以返回 [0, 1]

#### 解答

一看到这题目，直接用了最粗暴简单的方式解答：

```js
/**
 * @param {number[]} nums  
 * @param {number} target  
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    var arr = [];
    for(var i = 0; i < nums.length; i++) {
        for(var j = i+1; j < nums.length; j++) {
            if(nums[i] + nums[j] == target) {
                arr = [i, j]
                return arr;
            }
        }
    }
};
```

提交看结果以后，果然暴力的方式效率最低。。

![1](/img/algorithm/1.jpg)

#### 分析

上述解题方式，通过遍历每个元素x查找与target-x相等的元素。时间复杂度O(n^2)。

解法二：

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    var map = new Map();
    for(var i = 0; i < nums.length; i++) {
        map.set(nums[i], i)
    }
    for(var i = 0; i < nums.length; i++) {
        var vals = target - nums[i];
        var key = map.get(vals);
        if(key && key != i) {
            return [i, key];
        }
    }
};
```

![2](/img/algorithm/2.jpg)

可以看到耗时一下子提高了很多，时间复杂度变成了O(n)。

如果把遍历变成一次呢？遍历的同时往回查找是否有复合条件的目标元素。

解法三：

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    var map = new Map();
    for(var i = 0; i < nums.length; i++) {
        var vals = target - nums[i];
        var key = map.get(vals);
        if(key != undefined) {
            return [key, i];
        }
        map.set(nums[i], i);
    }

};
```

![3](/img/algorithm/3.jpg)

可以看到由于减少了一次遍历，耗时大大缩减，时间复杂度变成了O(1)。

#### 总结

1、 循环嵌套越少，耗时越短；
2、 使用键值对查找值比数组查找效率高许多。