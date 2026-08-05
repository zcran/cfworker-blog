---
title: "leetcode-回溯8"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## N 皇后

按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。

n 皇后问题 研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 n ，返回所有不同的 n 皇后问题 的解决方案。

每一种解法包含一个不同的 n 皇后问题 的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。


```
impl Solution {
    /// N 皇后问题 - 返回所有不同的解决方案
    ///
    /// # 示例
    /// ```
    /// 输入: n = 4
    /// 输出: [
    ///   [".Q..", "...Q", "Q...", "..Q."],
    ///   ["..Q.", "Q...", "...Q", ".Q.."]
    /// ]
    /// ```
    ///
    /// # 算法
    /// 回溯法：逐行放置皇后，使用三个集合记录冲突位置
    /// - 列冲突：同一列不能有两个皇后
    /// - 主对角线冲突：row - col 相同
    /// - 副对角线冲突：row + col 相同
    ///
    /// # 复杂度
    /// - 时间复杂度: O(n!)，实际远小于 n!
    /// - 空间复杂度: O(n)
    pub fn solve_n_queens(n: i32) -> Vec<Vec<String>> {
        let n = n as usize;
        let mut result = Vec::new();

        // 记录每行皇后所在的列位置（-1 表示未放置）
        let mut queens = vec![usize::MAX; n];

        // 使用数组替代 HashSet 提升性能
        let mut cols = vec![false; n];
        let mut diag1 = vec![false; 2 * n - 1]; // row - col + (n-1) 的偏移
        let mut diag2 = vec![false; 2 * n - 1]; // row + col

        // 预生成行模板
        let row_template = vec!['.'; n];

        Self::backtrack(
            &mut queens,
            &mut cols,
            &mut diag1,
            &mut diag2,
            &row_template,
            &mut result,
            0,
            n,
        );

        result
    }

    /// 回溯搜索函数
    fn backtrack(
        queens: &mut [usize],
        cols: &mut [bool],
        diag1: &mut [bool],
        diag2: &mut [bool],
        row_template: &[char],
        result: &mut Vec<Vec<String>>,
        row: usize,
        n: usize,
    ) {
        // 所有行都已放置皇后，生成棋盘
        if row == n {
            let mut board = Vec::with_capacity(n);
            for &col in queens.iter() {
                let mut line = row_template.to_vec();
                line[col] = 'Q';
                board.push(line.iter().collect());
            }
            result.push(board);
            return;
        }

        // 尝试在当前行的每一列放置皇后
        for col in 0..n {
            let d1_idx = row + col; // 副对角线索引
            let d2_idx = row + n - 1 - col; // 主对角线索引

            // 检查冲突
            if cols[col] || diag1[d1_idx] || diag2[d2_idx] {
                continue;
            }

            // 放置皇后
            queens[row] = col;
            cols[col] = true;
            diag1[d1_idx] = true;
            diag2[d2_idx] = true;

            // 递归下一行
            Self::backtrack(queens, cols, diag1, diag2, row_template, result, row + 1, n);

            // 回溯：撤销皇后
            cols[col] = false;
            diag1[d1_idx] = false;
            diag2[d2_idx] = false;
            // queens[row] 无需重置，会被覆盖
        }
    }
}
```
