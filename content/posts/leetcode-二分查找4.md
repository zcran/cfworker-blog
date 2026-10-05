---
title: "leetcode-二分查找4"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## x 的平方根

给你一个非负整数 x ，计算并返回 x 的 算术平方根 。

由于返回类型是整数，结果只保留 整数部分 ，小数部分将被 舍去 。

注意：不允许使用任何内置指数函数和算符，例如 pow(x, 0.5) 或者 x ** 0.5 。


```
impl Solution {
    pub fn my_sqrt(x: i32) -> i32 {
        const SQRT_I32_MAX: i32 = 46340; // floor(sqrt(i32::MAX))

        // 在开区间 (left, right) 中二分，寻找满足 m*m <= x 的最大 m
        let mut left = 0;
        let mut right = x.min(SQRT_I32_MAX) + 1;
        // 循环不变量：left^2 <= x < right^2
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if mid * mid <= x {
                left = mid;
            } else {
                right = mid;
            }
        }
        left
    }
}
```
