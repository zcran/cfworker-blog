---
title: "leetcode-贪心117"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## K 件物品的最大和

袋子中装有一些物品，每个物品上都标记着数字 1 、0 或 -1 。

给你四个非负整数 numOnes 、numZeros 、numNegOnes 和 k 。

袋子最初包含：

numOnes 件标记为 1 的物品。
numZeros 件标记为 0 的物品。
numNegOnes 件标记为 -1 的物品。

现计划从这些物品中恰好选出 k 件物品。返回所有可行方案中，物品上所标记数字之和的最大值。


```
impl Solution {
    pub fn k_items_with_maximum_sum(num_ones: i32, num_zeros: i32, num_neg_ones: i32, k: i32) -> i32 {
        // 贪心：优先选 1，再选 0，最后才选 -1
        // 前两种都不影响或增加和，超出部分只能用 -1 抵消
        num_ones.min(k) - (k - num_ones - num_zeros).max(0)
    }
}
```
