---
title: "leetcode-前缀和104"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 网格图操作后的最大分数

给你一个大小为 n x n 的二维矩阵 grid ，一开始所有格子都是白色的。一次操作中，你可以选择任意下标为 (i, j) 的格子，并将第 j 列中从最上面到第 i 行所有格子改成黑色。

如果格子 (i, j) 为白色，且左边或者右边的格子至少一个格子为黑色，那么我们将 grid[i][j] 加到最后网格图的总分中去。

请你返回执行任意次操作以后，最终网格图的 最大 总分数。


```
impl Solution {
    pub fn maximum_score(grid: Vec<Vec<i32>>) -> i64 {
        let n = grid.len();
        if n == 1 {
            return 0;
        }

        // 列前缀和: col_sum[c][r] = 第c列前r行的和
        let mut col_sum = vec![vec![0i64; n + 1]; n];
        for c in 0..n {
            for r in 0..n {
                col_sum[c][r + 1] = col_sum[c][r] + grid[r][c] as i64;
            }
        }

        // dp[i][curr_h][prev_h]: 处理前i列，当前列高度为curr_h，前一列高度为prev_h的最大分数
        let mut dp = vec![vec![vec![0i64; n + 1]; n + 1]; n];

        // 前缀最大值和前缀后缀最大值，用于优化转移
        let mut prefix_max = vec![vec![0i64; n + 1]; n + 1];
        let mut suffix_max = vec![vec![0i64; n + 1]; n + 1];

        for i in 1..n {
            // 转移
            for curr_h in 0..=n {
                for prev_h in 0..=n {
                    if curr_h <= prev_h {
                        // 当前列在上一列范围内，额外得分：上一列从curr_h到prev_h的部分
                        let extra = col_sum[i][prev_h] - col_sum[i][curr_h];
                        dp[i][curr_h][prev_h] = dp[i][curr_h][prev_h].max(suffix_max[prev_h][0] + extra);
                    } else {
                        // 当前列超出上一列，额外得分：前一列从prev_h到curr_h的部分
                        let extra = col_sum[i - 1][curr_h] - col_sum[i - 1][prev_h];
                        dp[i][curr_h][prev_h] = dp[i][curr_h][prev_h].max(
                            suffix_max[prev_h][curr_h].max(prefix_max[prev_h][curr_h] + extra)
                        );
                    }
                }
            }

            // 更新前缀最大值
            for curr_h in 0..=n {
                prefix_max[curr_h][0] = dp[i][curr_h][0];
                for prev_h in 1..=n {
                    let penalty = if prev_h > curr_h {
                        col_sum[i][prev_h] - col_sum[i][curr_h]
                    } else {
                        0
                    };
                    prefix_max[curr_h][prev_h] = prefix_max[curr_h][prev_h - 1]
                        .max(dp[i][curr_h][prev_h] - penalty);
                }
            }

            // 更新后缀最大值
            for curr_h in 0..=n {
                suffix_max[curr_h][n] = dp[i][curr_h][n];
                for prev_h in (0..n).rev() {
                    suffix_max[curr_h][prev_h] = suffix_max[curr_h][prev_h + 1]
                        .max(dp[i][curr_h][prev_h]);
                }
            }
        }

        // 答案：最后一列高度为0或n
        let last = n - 1;
        (0..=n)
            .map(|h| dp[last][n][h].max(dp[last][0][h]))
            .max()
            .unwrap_or(0)
    }
}
```
