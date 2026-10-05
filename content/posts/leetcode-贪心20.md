---
title: "leetcode-贪心20"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 买卖股票的最佳时机含手续费

给定一个整数数组 prices，其中 prices[i]表示第 i 天的股票价格 ；整数 fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。


```
impl Solution {
    pub fn max_profit(prices: Vec<i32>, fee: i32) -> i32 {
        // cash: 当前不持有股票时的最大利润
        // hold: 当前持有股票时的最大利润（买入时已计入成本，卖出时再扣手续费）
        let (mut cash, mut hold) = (0, i32::MIN / 2);

        for p in prices {
            // 先算卖出后的收益，再更新持有状态，避免同日重复交易
            let sold = hold + p - fee;
            cash = cash.max(sold);
            hold = hold.max(cash - p);
        }

        cash
    }
}
```
