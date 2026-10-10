---
title: "leetcode-数学1"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 阶乘后的零

给定一个整数 n ，返回 n! 结果中尾随零的数量。

提示 n! = n * (n - 1) * (n - 2) * ... * 3 * 2 * 1


```
impl Solution {
    /// 计算 n! 结果中尾随零的数量。
    ///
    /// 尾随零由因子 10 产生，而 10 = 2 × 5。
    /// 在 n! 中因子 2 的数量远多于 5，因此只需统计因子 5 的个数。
    ///
    /// 因子 5 的个数为：⌊n/5⌋ + ⌊n/25⌋ + ⌊n/125⌋ + ...
    pub fn trailing_zeroes(n: i32) -> i32 {
        let mut n = n;
        let mut ans = 0;
        while n > 0 {
            n /= 5;
            ans += n;
        }
        ans
    }
}
```
