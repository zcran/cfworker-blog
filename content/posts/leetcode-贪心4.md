---
title: "leetcode-贪心4"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 买卖股票的最佳时机 II

给你一个整数数组 prices ，其中 prices[i] 表示某支股票第 i 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 最多 只能持有 一股 股票。然而，你可以在 同一天 多次买卖该股票，但要确保你持有的股票不超过一股。

返回 你能获得的 最大 利润 。

```
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
        // 贪心：只要第二天价格高于当天，就在当天买入、第二天卖出
        // 所有上涨区间的利润之和即为最大利润
        prices
            .windows(2)
            .map(|w| (w[1] - w[0]).max(0))
            .sum()
    }
}
```
