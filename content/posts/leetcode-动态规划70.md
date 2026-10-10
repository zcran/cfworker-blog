---
title: "leetcode-动态规划70"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## K 次串联后最大子数组之和

给定一个整数数组 arr 和一个整数 k ，通过重复 k 次来修改数组。

例如，如果 arr = [1, 2] ， k = 3 ，那么修改后的数组将是 [1, 2, 1, 2, 1, 2] 。

返回修改后的数组中的最大的子数组之和。注意，子数组长度可以是 0，在这种情况下它的总和也是 0。

由于 结果可能会很大，需要返回结果对 10^9 + 7 取 模。


```
impl Solution {
    /// 1191. K 次串联后最大子数组之和
    ///
    /// 记 s = arr 的总和，mx = arr 的最大子数组和（允许空，故 ≥ 0）。
    /// 分情况：
    ///   - k == 1：答案就是单份 arr 的最大子数组和。
    ///   - k >= 2：最优子数组最多横跨两份 arr（首尾各取一段），
    ///     中间完整重复的 (k-2) 份只有当 s > 0 时才值得全部取上。
    ///     因此答案 = maxSubArray(arr+arr) + max(s, 0) * (k - 2)。
    ///
    /// 用一次遍历同时计算单份最大子数组和、总和、双份最大子数组和，
    /// 避免拼接数组或多次扫描。
    pub fn k_concatenation_max_sum(arr: Vec<i32>, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 单份 arr 的最大子数组和（允许空 → 初值 0）
        let mut best1 = 0i64;
        // arr+arr 的最大子数组和
        let mut best2 = 0i64;
        let mut cur1 = 0i64;
        let mut cur2 = 0i64;
        let mut sum = 0i64;

        for &x in &arr {
            let x = x as i64;
            sum += x;
            cur1 = cur1.max(0) + x;
            best1 = best1.max(cur1);
            cur2 = cur2.max(0) + x;
            best2 = best2.max(cur2);
        }
        // 第二遍模拟 arr 的第二份，用于横跨两份的最大子数组和
        for &x in &arr {
            let x = x as i64;
            cur2 = cur2.max(0) + x;
            best2 = best2.max(cur2);
        }

        let ans = if k == 1 {
            best1
        } else {
            best2 + sum.max(0) * (k as i64 - 2)
        };

        (ans % MOD) as i32
    }
}
```
