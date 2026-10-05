---
title: "leetcode-二分查找15"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 排列硬币

你总共有 n 枚硬币，并计划将它们按阶梯状排列。对于一个由 k 行组成的阶梯，其第 i 行必须正好有 i 枚硬币。阶梯的最后一行 可能 是不完整的。

给你一个数字 n ，计算并返回可形成 完整阶梯行 的总行数。


```
impl Solution {
    pub fn arrange_coins(n: i32) -> i32 {
        // 求满足 k*(k+1)/2 <= n 的最大 k
        // 在 [1, n] 中二分，使用 i64 避免乘法/加法溢出
        let n = n as i64;
        let (mut left, mut right) = (1i64, n);

        while left <= right {
            let mid = left + (right - left) / 2;
            if mid * (mid + 1) / 2 <= n {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        right as i32
    }
}
```
