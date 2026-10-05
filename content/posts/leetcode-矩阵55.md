---
title: "leetcode-矩阵55"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵中移动的最大次数

给你一个下标从 0 开始、大小为 m x n 的矩阵 grid ，矩阵由若干 正 整数组成。

你可以从矩阵第一列中的 任一 单元格出发，按以下方式遍历 grid ：

从单元格 (row, col) 可以移动到 (row - 1, col + 1)、(row, col + 1) 和 (row + 1, col + 1) 三个单元格中任一满足值 严格 大于当前单元格的单元格。

返回你在矩阵中能够 移动 的 最大 次数。




```
impl Solution {
    /// 逐列 DP：`reachable[i]` 表示当前列第 i 行是否可达。
    /// 初始第一列全部可达；每列由前一列的可达行向 (i-1,i,i+1) 且值严格更大的行转移。
    /// 若某列没有任何可达行，则答案是上一列的下标（已移动步数）。
    ///
    /// 优化：
    ///   - 用 `Vec<bool>` 代替 `HashSet<usize>`，O(m) 定长数组按位/按下标访问，比哈希快得多且省内存。
    ///   - 用 `try_into().unwrap_or(0)` 式的边界收缩避免 `saturating_sub` 带来的上界混淆。
    ///   - 记录 `last_reachable_col` 即可，无需每列判断后立即返回。
    pub fn max_moves(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        // 第一列所有行都可作为起点
        let mut reachable = vec![true; m];
        let mut last_col = 0; // 最后一列有可达行的列下标

        for j in 1..n {
            let mut next = vec![false; m];
            let mut any = false;

            for i in 0..m {
                if !reachable[i] {
                    continue;
                }
                // 可移动到前一列同行的 (i-1, i, i+1)
                let lo = i.saturating_sub(1);
                let hi = (i + 1).min(m - 1);
                for k in lo..=hi {
                    if grid[i][j - 1] < grid[k][j] {
                        next[k] = true;
                        any = true;
                    }
                }
            }

            if !any {
                // 无法继续移动，答案停在上一列
                return last_col as i32;
            }
            reachable = next;
            last_col = j;
        }

        last_col as i32
    }
}
```
