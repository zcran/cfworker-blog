---
title: "leetcode-贪心15"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 数组拆分

给定长度为 2n 的整数数组 nums ，你的任务是将这些数分成 n 对, 例如 (a1, b1), (a2, b2), ..., (an, bn) ，使得从 1 到 n 的 min(ai, bi) 总和最大。

返回该 最大总和 。


```
impl Solution {
    pub fn array_pair_sum(mut nums: Vec<i32>) -> i32 {
        // 贪心：排序后相邻两两配对，每对取较小值（即每对的第一个元素）
        // 这样能让所有较大值尽可能成为"较大"的一方，从而使 min 之和最大
        nums.sort_unstable();
        nums.iter().step_by(2).sum()
    }
}
```
