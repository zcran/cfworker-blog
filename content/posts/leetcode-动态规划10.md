---
title: "leetcode-动态规划10"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 买卖股票的最佳时机 III

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。


```
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
        const K: usize = 2;
        const NEG: i32 = i32::MIN / 2; // 防止加法溢出

        // f[j][0]：完成 j 笔交易后的最大利润（空仓）
        // f[j][1]：处于第 j 笔交易持仓状态时的最大利润
        let mut f = vec![[0, NEG]; K + 2];
        f[0][0] = NEG;

        for p in prices {
            // 逆序更新交易次数，保证每笔交易只被使用一次
            for j in (1..=K + 1).rev() {
                f[j][0] = f[j][0].max(f[j][1] + p);
                f[j][1] = f[j][1].max(f[j - 1][0] - p);
            }
        }

        f[K + 1][0]
    }
}
```
