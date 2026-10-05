---
title: "leetcode-广度优先搜索50"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 零钱兑换

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。

```
impl Solution {
    pub fn coin_change(coins: Vec<i32>, amount: i32) -> i32 {
        // dp[i] 表示凑成金额 i 所需的最少硬币数
        // 初始化为 amount + 1（比任何合法答案都大），便于后续取 min
        let amount = amount as usize;
        let mut dp = vec![amount as i32 + 1; amount + 1];
        dp[0] = 0;

        // 完全背包：外层遍历金额，内层遍历硬币
        for i in 1..=amount {
            for &c in &coins {
                let c = c as usize;
                if c <= i {
                    dp[i] = dp[i].min(dp[i - c] + 1);
                }
            }
        }

        // 若 dp[amount] 仍为初始值，说明无法凑成
        if dp[amount] > amount as i32 {
            -1
        } else {
            dp[amount]
        }
    }
}
```
