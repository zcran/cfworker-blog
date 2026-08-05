---
title: "leetcode-回溯27"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 目标和

给你一个非负整数数组 nums 和一个整数 target 。

向数组中的每个整数前添加 '+' 或 '-' ，然后串联起所有整数，可以构造一个 表达式 ：

例如，nums = [2, 1] ，可以在 2 之前添加 '+' ，在 1 之前添加 '-' ，然后串联起来得到表达式 "+2-1" 。

返回可以通过上述方法构造的、运算结果等于 target 的不同 表达式 的数目。


```
impl Solution {
    /// 返回通过添加 +/- 使表达式结果等于 target 的不同表达式数目
    ///
    /// 思路：转化为子集和问题
    /// 设正数和为 P，负数和为 N，则 P + N = sum，P - N = target
    /// 解得 P = (sum + target) / 2，即从 nums 中选若干数使其和为 P
    ///
    /// 使用 1D DP 优化空间：dp[j] 表示凑成和 j 的方案数
    pub fn find_target_sum_ways(nums: Vec<i32>, target: i32) -> i32 {
        let sum: i32 = nums.iter().sum();

        // target 必须在 [-sum, sum] 范围内，且 (sum + target) 必须为偶数
        if target.abs() > sum || (sum + target) % 2 != 0 {
            return 0;
        }

        let n = ((sum + target) / 2) as usize;
        let mut dp = vec![0; n + 1];
        dp[0] = 1; // 空集凑成 0 有 1 种方案

        for &num in &nums {
            // 逆序更新，避免重复使用当前元素
            for j in (num as usize..=n).rev() {
                dp[j] += dp[j - num as usize];
            }
        }

        dp[n]
    }
}
```
