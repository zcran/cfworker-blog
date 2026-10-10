---
title: "leetcode-数学69"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 船上可以装载的最大集装箱数量

给你一个正整数 n，表示船上的一个 n x n 的货物甲板。甲板上的每个单元格可以装载一个重量 恰好 为 w 的集装箱。

然而，如果将所有集装箱装载到甲板上，其总重量不能超过船的最大承载重量 maxWeight。

请返回可以装载到船上的 最大 集装箱数量。




```
impl Solution {
    pub fn max_containers(n: i32, w: i32, max_weight: i32) -> i32 {
        // 甲板最多容纳 n² 个集装箱；
        // 承重限制最多容纳 max_weight / w 个（向下取整）。
        // 两者取较小值即为答案。
        (n * n).min(max_weight / w)
    }
}
```
