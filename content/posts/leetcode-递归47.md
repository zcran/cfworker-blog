---
title: "leetcode-递归47"
date: 2026-05-26T10:00:19+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 递归乘法

递归乘法。 写一个递归函数，不使用 * 运算符， 实现两个正整数的相乘。可以使用加号、减号、位移，但要吝啬一些。


```
impl Solution {
    pub fn multiply(a: i32, b: i32) -> i32 {
       if(b==1) {return a;}
       else {return a + Self::multiply(a, b-1)};
    }
}
```
