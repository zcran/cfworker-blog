---
title: "leetcode-贪心73"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 雪糕的最大数量

夏日炎炎，小男孩 Tony 想买一些雪糕消消暑。

商店中新到 n 支雪糕，用长度为 n 的数组 costs 表示雪糕的定价，其中 costs[i] 表示第 i 支雪糕的现金价格。Tony 一共有 coins 现金可以用于消费，他想要买尽可能多的雪糕。

注意：Tony 可以按任意顺序购买雪糕。

给你价格数组 costs 和现金量 coins ，请你计算并返回 Tony 用 coins 现金能够买到的雪糕的 最大数量 。

你必须使用计数排序解决此问题。


```
impl Solution {
    pub fn max_ice_cream(costs: Vec<i32>, coins: i32) -> i32 {
        // 题目要求计数排序：价格范围有限（0..=100000），用频次数组代替普通排序
        const MAX_PRICE: usize = 100_001;
        let mut freq = [0i32; MAX_PRICE];
        for &c in &costs {
            freq[c as usize] += 1;
        }

        let mut remaining = coins;
        let mut count = 0;
        for price in 0..MAX_PRICE {
            if freq[price] == 0 {
                continue;
            }
            let cost = price as i32;
            // 当前价格最多能买 min(剩余钱/单价, 库存) 根
            let can_buy = (remaining / cost).min(freq[price]);
            count += can_buy;
            remaining -= can_buy * cost;
            if remaining < cost {
                // 剩余钱已不足以再买任何雪糕
                break;
            }
        }
        count
    }
}
```
