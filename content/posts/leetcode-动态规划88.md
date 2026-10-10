---
title: "leetcode-动态规划88"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 执行乘法运算的最大分数

给你两个长度分别 n 和 m 的整数数组 nums 和 multipliers ，其中 n >= m ，数组下标 从 1 开始 计数。

初始时，你的分数为 0 。你需要执行恰好 m 步操作。在第 i 步操作（从 1 开始 计数）中，需要：

选择数组 nums 开头处或者末尾处 的整数 x 。
你获得 multipliers[i] * x 分，并累加到你的分数中。
将 x 从数组 nums 中移除。

在执行 m 步操作后，返回 最大 分数。


```
impl Solution {
    pub fn maximum_score(nums: Vec<i32>, multipliers: Vec<i32>) -> i32 {
        let (n, m) = (nums.len(), multipliers.len());
        // dp[j]：当前累计步数下，已从 nums 左侧取 j 个元素时的最大分数
        // 总步数为 i 时，右侧取了 i - j 个元素
        let mut dp = vec![0i32; m + 1];

        for i in 1..=m {
            let a = multipliers[i - 1];

            // 边界：左侧取 i 个（全部从左侧取）
            dp[i] = dp[i - 1] + nums[i - 1] * a;

            // 中间状态：左侧取 j 个，右侧取 i - j 个
            // 从右向左更新，保证 dp[j] 和 dp[j-1] 使用的是上一轮的值
            for j in (1..i).rev() {
                // 上一轮左侧取 j 个，本轮从右侧取 nums[n - (i - j)]
                let from_right = dp[j] + nums[n - (i - j)] * a;
                // 上一轮左侧取 j-1 个，本轮从左侧取 nums[j - 1]
                let from_left = dp[j - 1] + nums[j - 1] * a;
                dp[j] = from_right.max(from_left);
            }

            // 边界：左侧取 0 个（全部从右侧取）
            dp[0] += nums[n - i] * a;
        }

        dp.into_iter().max().unwrap()
    }
}
```
