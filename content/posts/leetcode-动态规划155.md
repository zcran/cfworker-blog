---
title: "leetcode-动态规划155"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 买卖股票的最佳时机 V

给你一个整数数组 prices，其中 prices[i] 是第 i 天股票的价格（美元），以及一个整数 k。

你最多可以进行 k 笔交易，每笔交易可以是以下任一类型：

普通交易：在第 i 天买入，然后在之后的第 j 天卖出，其中 i < j。你的利润是 prices[j] - prices[i]。

做空交易：在第 i 天卖出，然后在之后的第 j 天买回，其中 i < j。你的利润是 prices[i] - prices[j]。

注意：你必须在开始下一笔交易之前完成当前交易。此外，你不能在已经进行买入或卖出操作的同一天再次进行买入或卖出操作。

通过进行 最多 k 笔交易，返回你可以获得的最大总利润。


```
impl Solution {
    pub fn maximum_profit(prices: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        let mut dp = vec![[0i64; 3]; k + 1];
        // dp[j] = [free, hold_long, hold_short]
        // free: 已完成 j 笔交易，无持仓
        // hold_long: 已完成 j-1 笔交易，持有普通多头（已买入）
        // hold_short: 已完成 j-1 笔交易，持有做空头寸（已卖出）

        // 初始状态
        for j in 1..=k {
            dp[j][1] = i64::MIN / 2;
            dp[j][2] = i64::MIN / 2;
        }

        for &p in &prices {
            let v = p as i64;
            // 从后往前更新，避免同一笔交易在同一天重复使用
            for j in (1..=k).rev() {
                let free = dp[j][0];
                let long = dp[j][1];
                let short = dp[j][2];
                let prev_free = dp[j - 1][0];

                dp[j][0] = free.max(long + v).max(short - v);
                dp[j][1] = long.max(prev_free - v);
                dp[j][2] = short.max(prev_free + v);
            }
        }

        dp[k][0]
    }
}
```
