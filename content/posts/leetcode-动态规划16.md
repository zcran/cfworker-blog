---
title: "leetcode-动态规划16"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 买卖股票的最佳时机含冷冻期

给定一个整数数组prices，其中第  prices[i] 表示第 i 天的股票价格 。​

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。




```
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
        // f0: 当前处于冷冻期后（不持有股票）的最大利润
        // f1: 当前持有股票时的最大利润
        // pre0: 前一天的 f0，用于处理冷冻期限制
        let (mut f0, mut f1, mut pre0) = (0, i32::MIN, 0);

        for p in prices {
            // 卖出股票后进入冷冻期，因此买入时只能依赖前一天的 f0
            let new_f0 = f0.max(f1 + p);
            f1 = f1.max(pre0 - p);
            pre0 = f0;
            f0 = new_f0;
        }

        f0
    }
}
```
