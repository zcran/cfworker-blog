---
title: "leetcode-模拟89"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 检查骑士巡视方案

骑士在一张 n x n 的棋盘上巡视。在 有效 的巡视方案中，骑士会从棋盘的 左上角 出发，并且访问棋盘上的每个格子 恰好一次 。

给你一个 n x n 的整数矩阵 grid ，由范围 [0, n * n - 1] 内的不同整数组成，其中 grid[row][col] 表示单元格 (row, col) 是骑士访问的第 grid[row][col] 个单元格。骑士的行动是从下标 0 开始的。

如果 grid 表示了骑士的有效巡视方案，返回 true；否则返回 false。

注意，骑士行动时可以垂直移动两个格子且水平移动一个格子，或水平移动两个格子且垂直移动一个格子。



```
impl Solution {
    pub fn check_valid_grid(grid: Vec<Vec<i32>>) -> bool {
        let n = grid.len();
        // 必须从 (0,0) 开始
        if grid[0][0] != 0 {
            return false;
        }

        // 建立坐标映射：步数 -> (行, 列)
        let mut pos = vec![(0usize, 0usize); n * n];
        for (r, row) in grid.iter().enumerate() {
            for (c, &step) in row.iter().enumerate() {
                pos[step as usize] = (r, c);
            }
        }

        // 检查每一步是否都是合法的日字走法
        for w in pos.windows(2) {
            let (r1, c1) = w[0];
            let (r2, c2) = w[1];
            let dr = r1.abs_diff(r2);
            let dc = c1.abs_diff(c2);
            // 骑士移动：(±1, ±2) 或 (±2, ±1)，即 dr*dc == 2
            if dr * dc != 2 {
                return false;
            }
        }

        true
    }
}
```
