---
title: "leetcode-前缀和60"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 从栈中取出 K 个硬币的最大面值和

一张桌子上总共有 n 个硬币 栈 。每个栈有 正整数 个带面值的硬币。

每一次操作中，你可以从任意一个栈的 顶部 取出 1 个硬币，从栈中移除它，并放入你的钱包里。

给你一个列表 piles ，其中 piles[i] 是一个整数数组，分别表示第 i 个栈里 从顶到底 的硬币面值。同时给你一个正整数 k ，请你返回在 恰好 进行 k 次操作的前提下，你钱包里硬币面值之和 最大为多少 。


```
impl Solution {
    pub fn max_value_of_coins(piles: Vec<Vec<i32>>, k: i32) -> i32 {
        let k = k as usize;
        // dp[t] 表示进行 t 次操作能获得的最大价值
        let mut dp = vec![-1; k + 1];
        dp[0] = 0;

        for pile in piles {
            // 每个栈只能选择一次，从后往前更新避免重复使用
            for taken in (1..=k).rev() {
                let mut prefix_sum = 0;
                // 尝试从当前栈中取 t 个硬币（必须连续从顶部取）
                for t in 1..=pile.len().min(k) {
                    prefix_sum += pile[t - 1];
                    if taken >= t && dp[taken - t] != -1 {
                        dp[taken] = dp[taken].max(dp[taken - t] + prefix_sum);
                    }
                }
            }
        }

        dp[k]
    }
}
```
