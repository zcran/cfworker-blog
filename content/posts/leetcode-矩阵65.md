---
title: "leetcode-矩阵65"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵中的最大得分

给你一个由 正整数 组成、大小为 m x n 的矩阵 grid。你可以从矩阵中的任一单元格移动到另一个位于正下方或正右侧的任意单元格（不必相邻）。从值为 c1 的单元格移动到值为 c2 的单元格的得分为 c2 - c1 。

你可以从 任一 单元格开始，并且必须至少移动一次。

返回你能得到的 最大 总得分。


```
impl Solution {
    pub fn max_score(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        // precol[j]：当前行之前，第 j 列能提供的最大“前缀收益”
        //            即 max(f[i'][j] - grid[i'][j])（i' < 当前行）
        // row_best：当前行中，左侧能提供的最大“前缀收益”
        //           即 max(f[i][j'] - grid[i][j'])（j' < 当前列）
        let neg_inf = i32::MIN / 2;
        let mut precol = vec![neg_inf; n];
        let mut ans = neg_inf;

        for i in 0..m {
            let mut row_best = neg_inf;

            for j in 0..n {
                let val = grid[i][j];

                // 从上方某列转移，或从左侧某列转移
                let mut best = neg_inf;
                if precol[j] != neg_inf {
                    best = best.max(val + precol[j]);
                }
                if row_best != neg_inf {
                    best = best.max(val + row_best);
                }

                ans = ans.max(best);

                // 用当前格更新“前缀收益”，供后续格子使用
                let gain = best.max(0) - val;
                precol[j] = precol[j].max(gain);
                row_best = row_best.max(gain);
            }
        }

        ans
    }
}
```
