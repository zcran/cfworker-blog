---
title: "leetcode-数学30"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 在区间范围内统计奇数数目

给你两个非负整数 low 和 high 。请你返回 low 和 high 之间（包括二者）奇数的数目。


```
impl Solution {
    /// 返回区间 [low, high] 内奇数的个数。
    ///
    /// 1..=x 中奇数的个数为 (x + 1) / 2（向下取整）。
    /// 用前缀和思想：区间奇数个数 = f(high) - f(low - 1)。
    pub fn count_odds(low: i32, high: i32) -> i32 {
        Self::odds_up_to(high) - Self::odds_up_to(low - 1)
    }

    /// 返回 1..=x 中奇数的个数，即 ceil(x / 2)。
    #[inline]
    const fn odds_up_to(x: i32) -> i32 {
        (x + 1) >> 1
    }
}
```
