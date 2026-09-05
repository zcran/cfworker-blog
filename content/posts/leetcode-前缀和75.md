---
title: "leetcode-前缀和75"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 找出中枢整数

给你一个正整数 n ，找出满足下述条件的 中枢整数 x ：

1 和 x 之间的所有元素之和等于 x 和 n 之间所有元素之和。

返回中枢整数 x 。如果不存在中枢整数，则返回 -1 。题目保证对于给定的输入，至多存在一个中枢整数。


```
impl Solution {
    pub fn pivot_integer(n: i32) -> i32 {
        // 1+2+...+n = n(n+1)/2
        // 设中枢整数为 x，则：
        // 1+2+...+(x-1) = (x+1)+...+n
        // 等价于：x(x+1)/2 - x = n(n+1)/2 - x(x+1)/2
        // 化简得：x² = n(n+1)/2
        let total_sum = n * (n + 1) / 2;
        let x = (total_sum as f64).sqrt() as i32;

        if x * x == total_sum { x } else { -1 }
    }
}
```
