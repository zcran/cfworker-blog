---
title: "leetcode-广度优先搜索3"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 零钱兑换

给你一个整数数组 coins ，表示不同面额的硬币；以及一个整数 amount ，表示总金额。

计算并返回可以凑成总金额所需的 最少的硬币个数 。如果没有任何一种硬币组合能组成总金额，返回 -1 。

你可以认为每种硬币的数量是无限的。


```
impl Solution {
    pub fn coin_change(coins: Vec<i32>, amount: i32) -> i32 {
        let amount = amount as usize;

        // dp[j] = 凑出金额 j 所需的最少硬币数
        // 用 amount + 1 作为“不可达”的哨兵值（最多只需 amount 枚 1 元硬币）
        let mut dp = vec![amount + 1; amount + 1];
        dp[0] = 0;

        // 完全背包：每种硬币可无限次使用
        for &coin in &coins {
            let c = coin as usize;
            for j in c..=amount {
                dp[j] = dp[j].min(dp[j - c] + 1);
            }
        }

        // 仍为哨兵值说明无法凑出
        if dp[amount] > amount {
            -1
        } else {
            dp[amount] as i32
        }
    }
}
```
