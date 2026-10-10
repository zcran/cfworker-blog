---
title: "leetcode-动态规划42"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 使用最小花费爬楼梯

给你一个整数数组 cost ，其中 cost[i] 是从楼梯第 i 个台阶向上爬需要支付的费用。

一旦你支付此费用，即可选择向上爬 一个 或者 两个 台阶。

你可以选择从下标为 0 或下标为 1 的台阶开始爬楼梯。

请你计算并返回达到楼梯顶部的 最低 花费，即最后一步之后的那个位置（下标 cost.length）。


```
impl Solution {
    /// 746. 使用最小花费爬楼梯
    ///
    /// dp[i] 表示到达第 i 个台阶的最低花费。
    /// 由于可以一次爬 1 或 2 个台阶，dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])。
    /// 滚动变量 f0 = dp[i-2]，f1 = dp[i-1]，空间优化到 O(1)。
    pub fn min_cost_climbing_stairs(cost: Vec<i32>) -> i32 {
        let (mut f0, mut f1) = (0, 0); // 起点可在第 0 或第 1 个台阶，初始花费均为 0
        for i in 1..cost.len() {
            let new_f = (f1 + cost[i]).min(f0 + cost[i - 1]);
            f0 = f1;
            f1 = new_f;
        }
        f1
    }
}
```
