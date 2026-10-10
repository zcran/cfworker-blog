---
title: "leetcode-动态规划13"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 买卖股票的最佳时机 IV

给你一个整数数组 prices 和一个整数 k ，其中 prices[i] 是某支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。也就是说，你最多可以买 k 次，卖 k 次。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。




```
impl Solution {
    pub fn max_profit(k: i32, prices: Vec<i32>) -> i32 {
        let n = prices.len();
        // 交易次数不可能超过 n/2，超过则等价于无限次交易
        let k = (k as usize).min(n / 2);

        // buy[j]：完成至多 j-1 笔交易、当前持有股票时的最大利润
        // sell[j]：完成至多 j 笔交易、当前不持有股票时的最大利润
        const NEG: i32 = i32::MIN / 2;
        let mut buy = vec![NEG; k + 1];
        let mut sell = vec![0; k + 1];

        for p in prices {
            // 逆序更新，保证每笔交易只依赖上一轮的状态
            for j in (1..=k).rev() {
                sell[j] = sell[j].max(buy[j] + p);      // 卖出
                buy[j] = buy[j].max(sell[j - 1] - p);   // 买入
            }
        }

        sell[k]
    }
}
```
