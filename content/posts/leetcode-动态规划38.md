---
title: "leetcode-动态规划38"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 骑士在棋盘上的概率

在一个 n x n 的国际象棋棋盘上，一个骑士从单元格 (row, column) 开始，并尝试进行 k 次移动。行和列是 从 0 开始 的，所以左上单元格是 (0,0) ，右下单元格是 (n - 1, n - 1) 。

象棋骑士有8种可能的走法，如下图所示。每次移动在基本方向上是两个单元格，然后在正交方向上是一个单元格。

每次骑士要移动时，它都会随机从8种可能的移动中选择一种(即使棋子会离开棋盘)，然后移动到那里。

骑士继续移动，直到它走了 k 步或离开了棋盘。

返回 骑士在棋盘停止移动后仍留在棋盘上的概率 。

```
const DIRS: [(i32, i32); 8] = [
    (-2, -1), (-2, 1), (2, -1), (2, 1),
    (-1, -2), (-1, 2), (1, -2), (1, 2),
];

impl Solution {
    pub fn knight_probability(n: i32, k: i32, row: i32, column: i32) -> f64 {
        let n = n as usize;
        let k = k as usize;

        // dp[i][j]：当前步数下，骑士位于 (i, j) 时最终仍留在棋盘上的概率
        let mut dp = vec![vec![1.0f64; n]; n];

        // 用两个二维数组滚动，省去 dp 的步数维度
        for _ in 0..k {
            let mut next = vec![vec![0.0f64; n]; n];
            for i in 0..n {
                for j in 0..n {
                    let mut prob = 0.0;
                    for (dx, dy) in DIRS {
                        let ni = i as i32 + dx;
                        let nj = j as i32 + dy;
                        // 只有落在棋盘内的走法才保留概率
                        if ni >= 0 && ni < n as i32 && nj >= 0 && nj < n as i32 {
                            prob += dp[ni as usize][nj as usize] / 8.0;
                        }
                    }
                    next[i][j] = prob;
                }
            }
            dp = next;
        }

        dp[row as usize][column as usize]
    }
}
```
