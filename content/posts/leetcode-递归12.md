---
title: "leetcode-递归12"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 2 的幂


给你一个整数 n，请你判断该整数是否是 2 的幂次方。如果是，返回 true ；否则，返回 false 。

如果存在一个整数 x 使得 n == 2^x ，则认为 n 是 2 的幂次方。



```
impl Solution {
    pub fn is_power_of_two(n: i32) -> bool {
        n>0 && n&(n-1) ==0
    }
}
```
