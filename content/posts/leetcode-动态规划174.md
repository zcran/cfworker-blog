---
title: "leetcode-动态规划174"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 乘以系数后最大子数组和

给你一个整数数组 nums 和一个正整数 k。

你必须选择 nums 的一个 子数组 并执行以下操作之一：

1. 将所选子数组中的每个数字乘以 k。
2. 将所选子数组中的每个数字除以 k。
    · 当正数除以 k 时，除法结果 向下取整。
    · 当负数除以 k 时，除法结果 向上取整。

返回结果数组中 非空 子数组的 最大 可能和。

注意，用于执行操作的 子数组 与用于求和的 子数组 可以是 不同 的。

子数组 是数组中一段连续的 非空 元素序列。


```
impl Solution {
    pub fn max_subarray_sum(nums: Vec<i32>, k: i32) -> i64 {
        // 对给定变换求最大子数组和。
        // f0: 尚未开始变换的最大子数组和
        // f1: 正在变换区间内的最大子数组和
        // f2: 变换区间已结束的最大子数组和
        let solve = |is_mul: bool| -> i64 {
            let mut f0: i64 = 0;
            let mut f1: i64 = 0;
            let mut f2: i64 = 0;
            let mut res = i64::MIN;

            for &x in &nums {
                let x = x as i64;
                // 变换后的值：乘法直接乘 k；除法按题意向零方向取整（Rust 的 / 已符合）
                let y = if is_mul { x * k as i64 } else { x / k as i64 };

                // 先算 f2（依赖旧的 f1, f2）
                f2 = f1.max(f2) + x;
                // f1 依赖旧的 f0, f1
                f1 = f0.max(f1).max(0) + y;
                // f0 依赖旧的 f0
                f0 = f0.max(0) + x;

                res = res.max(f1).max(f2);
            }

            res
        };

        solve(true).max(solve(false))
    }
}
```
