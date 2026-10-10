---
title: "leetcode-动态规划8"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 三角形最小路径和

给定一个三角形 triangle ，找出自顶向下的最小路径和。

每一步只能移动到下一行中相邻的结点上。相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。也就是说，如果正位于当前行的下标 i ，那么下一步可以移动到下一行的下标 i 或 i + 1 。


```
impl Solution {
    pub fn minimum_total(triangle: Vec<Vec<i32>>) -> i32 {
        let n = triangle.len();

        // dp[j]：到达当前行第 j 个位置的最小路径和
        // 从底部向上递推，可原地覆盖，无需二维数组
        let mut dp = triangle[n - 1].clone();

        for i in (0..n - 1).rev() {
            for j in 0..=i {
                // 从下方相邻的两个位置中取较小值，加上当前值
                dp[j] = dp[j].min(dp[j + 1]) + triangle[i][j];
            }
        }

        dp[0]
    }
}
```
