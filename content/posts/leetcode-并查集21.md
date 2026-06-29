---
title: "leetcode-并查集21"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 飞地的数量

给你一个大小为 m x n 的二进制矩阵 grid ，其中 0 表示一个海洋单元格、1 表示一个陆地单元格。

一次 移动 是指从一个陆地单元格走到另一个相邻（上、下、左、右）的陆地单元格或跨过 grid 的边界。

返回网格中 无法 在任意次数的移动中离开网格边界的陆地单元格的数量。


```
impl Solution {
    /// 返回无法离开网格边界的陆地单元格数量。
    /// 从边界陆地开始 BFS/DFS，将所有能到达边界的陆地标记为已访问（置0），
    /// 最后统计剩余的 1 的数量。
    pub fn num_enclaves(mut grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let mut stack = Vec::new();

        // 1. 将所有边界上的陆地入栈，并统计总陆地数
        let mut total_land = 0;
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] == 1 {
                    total_land += 1;
                    // 边界陆地：第一行/最后一行/第一列/最后一列
                    if i == 0 || i == m - 1 || j == 0 || j == n - 1 {
                        stack.push((i, j));
                    }
                }
            }
        }

        // 2. DFS 标记所有能到达边界的陆地
        while let Some((r, c)) = stack.pop() {
            // 检查是否越界或已访问（0 表示海洋或已访问）
            if r >= m || c >= n || grid[r][c] == 0 {
                continue;
            }
            // 标记为已访问
            grid[r][c] = 0;
            total_land -= 1;

            // 四个方向入栈
            if r > 0 {
                stack.push((r - 1, c));
            }
            if r + 1 < m {
                stack.push((r + 1, c));
            }
            if c > 0 {
                stack.push((r, c - 1));
            }
            if c + 1 < n {
                stack.push((r, c + 1));
            }
        }

        total_land
    }
}
```
