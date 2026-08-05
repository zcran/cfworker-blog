---
title: "leetcode-枚举26"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 买钢笔和铅笔的方案数

给你一个整数 total ，表示你拥有的总钱数。同时给你两个整数 cost1 和 cost2 ，分别表示一支钢笔和一支铅笔的价格。你可以花费你部分或者全部的钱，去买任意数目的两种笔。

请你返回购买钢笔和铅笔的 不同方案数目 。


```
impl Solution {
    pub fn ways_to_buy_pens_pencils(total: i32, cost1: i32, cost2: i32) -> i64 {
        // 将总金额和价格转为 i64，避免中间乘法溢出
        let total = total as i64;
        let expensive = cost1.max(cost2) as i64; // 较贵的钢笔/铅笔
        let cheap     = cost1.min(cost2) as i64; // 较便宜的

        // 枚举购买较贵笔的数量 c（从 0 到最大可能）
        let max_expensive = total / expensive;
        let mut answer = 0;

        for c in 0..=max_expensive {
            // 剩余金额可以购买便宜笔的数量（整数除法向下取整）
            // +1 表示不买便宜笔的情况
            answer += (total - c * expensive) / cheap + 1;
        }

        answer
    }
}
```
