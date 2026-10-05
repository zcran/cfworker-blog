---
title: "leetcode-贪心120"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 购买两块巧克力

给你一个整数数组 prices ，它表示一个商店里若干巧克力的价格。同时给你一个整数 money ，表示你一开始拥有的钱数。

你必须购买 恰好 两块巧克力，而且剩余的钱数必须是 非负数 。同时你想最小化购买两块巧克力的总花费。

请你返回在购买两块巧克力后，最多能剩下多少钱。如果购买任意两块巧克力都超过了你拥有的钱，请你返回 money 。注意剩余钱数必须是非负数。


```
impl Solution {
    pub fn buy_choco(mut prices: Vec<i32>, money: i32) -> i32 {
        // 排序后取最便宜的两块巧克力，即为最小花费
        prices.sort_unstable();
        let cost = prices[0] + prices[1];

        // 若买不起，则一分不花，返回原有钱数
        if cost > money {
            money
        } else {
            money - cost
        }
    }
}
```
