---
title: "leetcode-堆28"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 销售价值减少的颜色球

你有一些球的库存 inventory ，里面包含着不同颜色的球。一个顾客想要 任意颜色 总数为 orders 的球。

这位顾客有一种特殊的方式衡量球的价值：每个球的价值是目前剩下的 同色球 的数目。比方说还剩下 6 个黄球，那么顾客买第一个黄球的时候该黄球的价值为 6 。这笔交易以后，只剩下 5 个黄球了，所以下一个黄球的价值为 5 （也就是球的价值随着顾客购买同色球是递减的）

给你整数数组 inventory ，其中 inventory[i] 表示第 i 种颜色球一开始的数目。同时给你整数 orders ，表示顾客总共想买的球数目。你可以按照 任意顺序 卖球。

请你返回卖了 orders 个球以后 最大 总价值之和。由于答案可能会很大，请你返回答案对 10^9 + 7 取余数 的结果。


```
use std::cmp;

impl Solution {
    pub fn max_profit(inventory: Vec<i32>, orders: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let orders = orders as i64;

        // Binary search: find the highest price `p` where
        // count of balls with value >= p is at least `orders`.
        let mut lo = 0i64;
        let mut hi = *inventory.iter().max().unwrap() as i64;
        let mut threshold = 0i64;

        while lo <= hi {
            let mid = lo + (hi - lo) / 2;
            let mut sellable = 0i64;
            for &iv in &inventory {
                sellable += cmp::max(0, iv as i64 - mid);
            }
            if sellable >= orders {
                threshold = mid;
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }

        // `threshold + 1` is the minimum price any sold ball reaches.
        // Sell all balls above this price, then the rest at this price.
        let floor = threshold + 1;
        let mut sold = 0i64;
        let mut profit = 0i64;

        for &iv in &inventory {
            let iv = iv as i64;
            if iv > floor {
                let cnt = iv - floor;
                sold += cnt;
                // Sum of prices floor+1 .. iv = (floor + 1 + iv) * cnt / 2
                profit += (floor + 1 + iv) * cnt / 2;
                profit %= MOD;
            }
        }

        // Remaining orders are filled at `floor` price
        profit += (orders - sold) * floor;
        (profit % MOD) as i32
    }
}
```
