---
title: "leetcode-位运算83"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 恰好一对连续置位

给你一个整数 n 。

如果其二进制表示中 恰好 仅包含 一对 相邻的置位 ，则返回 true ，否则返回 false 。

整数中的 置位 是指其 二进制 表示中的 1 。


```
impl Solution {
    pub fn consecutive_set_bits(n: i32) -> bool {
        // n & (n >> 1)：第 i 位为 1 当且仅当 n 的第 i 和 i+1 位都是 1，
        // 即标记了所有"相邻置位对"的起始位置。
        // 恰好一对相邻置位 ⇔ 结果中恰好有一个 1 ⇔ 结果 > 0 且是 2 的幂。
        let m = n & (n >> 1);
        m > 0 && (m & (m - 1)) == 0
    }
}
```
