---
title: "leetcode-动态规划20"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 组合总和 Ⅳ

给你一个由 不同 整数组成的数组 nums ，和一个目标整数 target 。请你从 nums 中找出并返回总和为 target 的元素组合的个数。

题目数据保证答案符合 32 位整数范围。


```
impl Solution {
    pub fn combination_sum4(nums: Vec<i32>, target: i32) -> i32 {
        let target = target as usize;

        // dp[i]：凑出总和 i 的组合个数
        // 外层枚举容量、内层枚举物品，顺序不同视为不同组合
        let mut dp = vec![0i32; target + 1];
        dp[0] = 1;

        for i in 1..=target {
            for &x in &nums {
                let x = x as usize;
                if x <= i {
                    dp[i] += dp[i - x];
                }
            }
        }

        dp[target]
    }
}
```
