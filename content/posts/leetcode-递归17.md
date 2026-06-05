---
title: "leetcode-递归17"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 4的幂


给定一个整数，写一个函数来判断它是否是 4 的幂次方。如果是，返回 true ；否则，返回 false 。

整数 n 是 4 的幂次方需满足：存在整数 x 使得 n == 4^x

```
impl Solution {
    pub fn is_power_of_four(n: i32) -> bool {
        !n.is_negative() && (n as u32).is_power_of_two() && n.trailing_zeros().is_multiple_of(2)
    }
}

```
