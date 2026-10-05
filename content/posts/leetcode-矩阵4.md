---
title: "leetcode-矩阵4"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 最小路径和

给定一个包含非负整数的 m x n 网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步。


```
impl Solution {
    pub fn min_path_sum(grid: Vec<Vec<i32>>) -> i32 {
        if grid.is_empty() || grid[0].is_empty() {
            return 0;
        }

        let rows = grid.len();
        let cols = grid[0].len();

        // 一维滚动数组：dp[j] 表示到达当前行第 j 列的最小路径和。
        // 初始化为第一行的前缀和（只能从左边过来）。
        let mut dp = vec![0; cols];
        dp[0] = grid[0][0];
        for j in 1..cols {
            dp[j] = dp[j - 1] + grid[0][j];
        }

        for i in 1..rows {
            // 第一列只能从上方过来，直接累加
            dp[0] += grid[i][0];
            for j in 1..cols {
                // dp[j] 是上一行同列（从上方来），dp[j-1] 是当前行前一列（从左边来）
                dp[j] = dp[j].min(dp[j - 1]) + grid[i][j];
            }
        }

        dp[cols - 1]
    }
}
```
