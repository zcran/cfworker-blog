---
title: "leetcode-动态规划28"
date: 2026-10-05T20:39:55+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 零钱兑换 II

给你一个整数数组 coins 表示不同面额的硬币，另给一个整数 amount 表示总金额。

请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 0 。

假设每一种面额的硬币有无限个。

题目数据 保证 最终 结果符合 32 位 带符号整数。


```
impl Solution {
    pub fn change(amount: i32, coins: Vec<i32>) -> i32 {
        let amount = amount as usize;

        // dp[i]：凑出金额 i 的硬币组合数
        let mut dp = vec![0i32; amount + 1];
        dp[0] = 1; // 凑出 0 元只有一种方式：不选任何硬币

        // 外层枚举硬币、内层枚举金额，保证组合不重复（硬币顺序无关）
        for c in coins {
            let c = c as usize;
            for i in c..=amount {
                dp[i] += dp[i - c];
            }
        }

        dp[amount]
    }
}
```
