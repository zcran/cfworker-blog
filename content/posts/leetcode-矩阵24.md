---
title: "leetcode-矩阵24"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 下降路径最小和 II

给你一个 n x n 整数矩阵 grid ，请你返回 非零偏移下降路径 数字和的最小值。

非零偏移下降路径 定义为：从 grid 数组中的每一行选择一个数字，且按顺序选出来的数字中，相邻数字不在原数组的同一列。


```
impl Solution {
    pub fn min_falling_path_sum(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid[0].len();
        // dp[j] 表示到达当前行第 j 列的最小路径和
        let mut dp = grid[0].clone();

        for row in grid.iter().skip(1) {
            // 找出上一行 dp 中的最小值、次小值及其对应下标
            // 若当前列与最小值下标相同，则只能选次小值，否则选最小值
            let mut min1 = i32::MAX; // 最小值
            let mut min2 = i32::MAX; // 次小值
            let mut min_idx = usize::MAX;

            for (j, &v) in dp.iter().enumerate() {
                if v < min1 {
                    min2 = min1;
                    min1 = v;
                    min_idx = j;
                } else if v < min2 {
                    min2 = v;
                }
            }

            // 原地更新 dp，省去额外数组
            for j in 0..n {
                let best = if j == min_idx { min2 } else { min1 };
                dp[j] = best + row[j];
            }
        }

        *dp.iter().min().unwrap()
    }
}
```
