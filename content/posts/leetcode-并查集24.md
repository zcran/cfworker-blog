---
title: "leetcode-并查集24"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 统计封闭岛屿的数目

二维矩阵 grid 由 0 （土地）和 1 （水）组成。岛是由最大的4个方向连通的 0 组成的群，封闭岛是一个 完全 由1包围（左、上、右、下）的岛。

请返回 封闭岛屿 的数目。


```
impl Solution {
    /// 返回封闭岛屿的数量。
    /// 封闭岛屿：完全被水（1）包围的陆地（0）连通区域。
    /// 从边界上的陆地开始 DFS，标记所有能到达边界的陆地（非封闭），
    /// 然后统计剩余的未被标记的陆地连通块数量。
    pub fn closed_island(mut grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let mut count = 0;

        /// DFS 标记所有与当前陆地相连的陆地
        fn dfs(grid: &mut Vec<Vec<i32>>, r: usize, c: usize) {
            if r >= grid.len() || c >= grid[0].len() || grid[r][c] != 0 {
                return;
            }
            grid[r][c] = 1; // 标记为水，避免重复访问

            // 四个方向
            if r > 0 {
                dfs(grid, r - 1, c);
            }
            if r + 1 < grid.len() {
                dfs(grid, r + 1, c);
            }
            if c > 0 {
                dfs(grid, r, c - 1);
            }
            if c + 1 < grid[0].len() {
                dfs(grid, r, c + 1);
            }
        }

        // 1. 从边界上的陆地开始 DFS，标记所有非封闭岛屿
        for i in 0..m {
            if grid[i][0] == 0 {
                dfs(&mut grid, i, 0);
            }
            if grid[i][n - 1] == 0 {
                dfs(&mut grid, i, n - 1);
            }
        }
        for j in 0..n {
            if grid[0][j] == 0 {
                dfs(&mut grid, 0, j);
            }
            if grid[m - 1][j] == 0 {
                dfs(&mut grid, m - 1, j);
            }
        }

        // 2. 统计剩余的陆地连通块（即为封闭岛屿）
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] == 0 {
                    count += 1;
                    dfs(&mut grid, i, j); // 标记整个岛屿，避免重复统计
                }
            }
        }

        count
    }
}
```
