---
layout:     post
title:      "[今日算法] 无重复字符的最长子串"
subtitle:   ""
author:     "wml"
header-img: "img/algorithm/bg.jpg"
header-mask:  0.5
catalog: true
tags:

    - 算法
---

#### 题目描述

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:

> 输入: "abcabcbb"
> 输出: 3
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

示例 2:

> 输入: "bbbbb"
> 输出: 1
> 解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

示例 3:

> 输入: "pwwkew"
> 输出: 3
> 解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
> 请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

#### 难度

中等

#### 解答

这题花了些时间，最开始想到的是用暴力解决法双层嵌套循环，但这个肯定效率低，想着肯定有更优解，尝试了Map方法，发现对于清除指定位置元素并不友好，所以直接用的字符串查询。

```js
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    let maxLen = 0;
    let str = ''
    for(var i = 0; i< s.length; i++) {
        let idx = str.indexOf(s[i]);
        if(idx != -1) {
            str = str.slice(idx+1)  
        }
        str += s[i];

        maxLen = Math.max(str.length, maxLen);
    }
    return maxLen;
};
```

提交结果

![1](/img/algorithm/day2-1.jpg)

#### 分析

解法一：

依旧是暴力法，两层for循环嵌套，遍历所有的字符，给出所有可能的子字符串，并得出最大长度的子串；由于这种方法效率低，耗时长，时间复杂度O(n^3)所以不就不详述了。

解法二：

也是上面采用的解法，按解析这个解法称之为滑动窗口。给定一个

```js
function lengthOfLongestSubstring(s) {
debugger
    var n = s.length,ans = 0;
    var map = new Map();
    for(var j=0, i=0; j<n; j++) {
        if(map.get(s.charAt(j))) {
            i = Math.max(map.get(s.charAt(j)), i)
        }
        ans = Math.max(ans, j-i+1);
        map.set(s.charAt(j), j+1);
    }
    return ans;
}
```