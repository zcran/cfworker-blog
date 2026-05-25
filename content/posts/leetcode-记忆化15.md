---
title: "leetcode-记忆化15"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 第 N 个泰波那契数


泰波那契序列 Tn 定义如下：

T0 = 0, T1 = 1, T2 = 1, 且在 n >= 0 的条件下 Tn+3 = Tn + Tn+1 + Tn+2

给你整数 n，请返回第 n 个泰波那契数 Tn 的值。

```
impl Solution {
    pub fn tribonacci(n: i32) -> i32 {
        (0..n).fold((0, 1, 1), |(a, b, c), _| (b, c, a + b + c)).0
    }
}
```
