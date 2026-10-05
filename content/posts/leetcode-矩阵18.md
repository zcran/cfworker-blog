---
title: "leetcode-矩阵18"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 翻转矩阵后的得分

给你一个大小为 m x n 的二元矩阵 grid ，矩阵中每个元素的值为 0 或 1 。

一次 移动 是指选择任一行或列，并转换该行或列中的每一个值：将所有 0 都更改为 1，将所有 1 都更改为 0。

在做出任意次数的移动后，将该矩阵的每一行都按照二进制数来解释，矩阵的 得分 就是这些数字的总和。

在执行任意次 移动 后（含 0 次），返回可能的最高分数。


```
impl Solution {
    pub fn matrix_score(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        // 贪心策略：
        // 1. 每行第一个元素（最高位）必须为 1，否则翻转该行。
        //    这样第一列全为 1，贡献 m * 2^(n-1)。
        // 2. 对之后的每一列，通过翻转子列让该列 1 的数量尽量多。
        //    由于每行是否翻转已由首列决定，列 j 中实际为 1 的数量可以计算出来，
        //    取 max(cnt, m - cnt) 即可。
        let mut score = m as i32 * (1 << (n - 1));

        for j in 1..n {
            let mut ones = 0i32;
            for i in 0..m {
                // 若该行首列为 1，说明该行未翻转，直接用 grid[i][j]；
                // 若首列为 0，说明该行会被翻转，实际值为 1 - grid[i][j]。
                ones += if grid[i][0] == 1 { grid[i][j] } else { 1 - grid[i][j] };
            }
            // 该列取 1 较多的一种（可通过列翻转实现）
            let best = ones.max(m as i32 - ones);
            score += best * (1 << (n - j - 1));
        }

        score
    }
}
```
