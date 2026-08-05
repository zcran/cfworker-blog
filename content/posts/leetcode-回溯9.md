---
title: "leetcode-回溯9"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## N 皇后 II

n 皇后问题 研究的是如何将 n 个皇后放置在 n × n 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 n ，返回 n 皇后问题 不同的解决方案的数量。


```
impl Solution {
    pub fn total_n_queens(n: i32) -> i32 {
        // 使用数组代替 HashSet，提升性能
        // col[i] 表示第 i 列是否被占用
        // diag1[i] 表示主对角线（row - col + n - 1）是否被占用
        // diag2[i] 表示副对角线（row + col）是否被占用
        let n = n as usize;
        let mut col = vec![false; n];
        let mut diag1 = vec![false; 2 * n - 1];
        let mut diag2 = vec![false; 2 * n - 1];

        Self::backtrack(0, n, &mut col, &mut diag1, &mut diag2)
    }

    fn backtrack(
        row: usize,
        n: usize,
        col: &mut [bool],
        diag1: &mut [bool],
        diag2: &mut [bool],
    ) -> i32 {
        // 所有行都放置完毕，找到一个有效解
        if row == n {
            return 1;
        }

        let mut count = 0;
        // 尝试在当前行的每一列放置皇后
        for c in 0..n {
            let d1 = row + c;           // 副对角线索引
            let d2 = row + n - 1 - c;   // 主对角线索引（确保非负）

            // 检查当前位置是否与已放置的皇后冲突
            if col[c] || diag1[d1] || diag2[d2] {
                continue;
            }

            // 放置皇后
            col[c] = true;
            diag1[d1] = true;
            diag2[d2] = true;

            // 递归处理下一行
            count += Self::backtrack(row + 1, n, col, diag1, diag2);

            // 回溯：移除皇后
            col[c] = false;
            diag1[d1] = false;
            diag2[d2] = false;
        }

        count
    }
}
```
