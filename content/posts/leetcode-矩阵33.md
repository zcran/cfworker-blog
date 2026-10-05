---
title: "leetcode-矩阵33"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵的最大非负积

给你一个大小为 m x n 的矩阵 grid 。最初，你位于左上角 (0, 0) ，每一步，你可以在矩阵中 向右 或 向下 移动。

在从左上角 (0, 0) 开始到右下角 (m - 1, n - 1) 结束的所有路径中，找出具有 最大非负积 的路径。路径的积是沿路径访问的单元格中所有整数的乘积。

返回 最大非负积 对 10^9 + 7 取余 的结果。如果最大积为 负数 ，则返回 -1 。

注意，取余是在得到最大积之后执行的。


```
impl Solution {
    pub fn max_product_path(grid: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let m = grid.len();
        let n = grid[0].len();

        // max_dp[i][j] / min_dp[i][j]：到达 (i,j) 的路径乘积的最大值 / 最小值
        // 由于负负得正，需要同时维护最大值和最小值
        let mut max_dp = vec![vec![0i64; n]; m];
        let mut min_dp = vec![vec![0i64; n]; m];
        max_dp[0][0] = grid[0][0] as i64;
        min_dp[0][0] = grid[0][0] as i64;

        // 第一行只能从左边来
        for j in 1..n {
            let v = grid[0][j] as i64;
            max_dp[0][j] = max_dp[0][j - 1] * v;
            min_dp[0][j] = max_dp[0][j];
        }
        // 第一列只能从上边来
        for i in 1..m {
            let v = grid[i][0] as i64;
            max_dp[i][0] = max_dp[i - 1][0] * v;
            min_dp[i][0] = max_dp[i][0];
        }

        for i in 1..m {
            for j in 1..n {
                let v = grid[i][j] as i64;
                // 左边和上边中乘积的最大值与最小值
                let max_prev = max_dp[i][j - 1].max(max_dp[i - 1][j]);
                let min_prev = min_dp[i][j - 1].min(min_dp[i - 1][j]);
                if v >= 0 {
                    max_dp[i][j] = max_prev * v;
                    min_dp[i][j] = min_prev * v;
                } else {
                    // 负数会使最大值与最小值互换
                    max_dp[i][j] = min_prev * v;
                    min_dp[i][j] = max_prev * v;
                }
            }
        }

        let result = max_dp[m - 1][n - 1];
        if result < 0 {
            -1
        } else {
            (result % MOD) as i32
        }
    }
}
```
