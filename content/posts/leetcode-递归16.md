---
title: "leetcode-递归16"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


##  3 的幂

给定一个整数，写一个函数来判断它是否是 3 的幂次方。如果是，返回 true ；否则，返回 false 。

整数 n 是 3 的幂次方需满足：存在整数 x 使得 n == 3^x


```
impl Solution {
    pub fn is_power_of_three(n: i32) -> bool {
        n > 0 && 1162261467 % n == 0
    }
}
```
