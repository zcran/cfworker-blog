---
title: "leetcode-并查集3"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 岛屿数量

给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。


```
impl Solution {
    pub fn num_islands(mut grid: Vec<Vec<char>>) -> i32 {
        /// 深度优先搜索，将当前岛屿的所有陆地标记为已访问（'0'）
        fn dfs(grid: &mut [Vec<char>], i: usize, j: usize) {
            // 边界检查或已访问/水域则返回
            if i >= grid.len() || j >= grid[i].len() || grid[i][j] == '0' {
                return;
            }

            // 标记为已访问（沉没岛屿）
            grid[i][j] = '0';

            // 探索四个方向
            dfs(grid, i + 1, j); // 下
            dfs(grid, i, j + 1); // 右
            if i > 0 { dfs(grid, i - 1, j); } // 上
            if j > 0 { dfs(grid, i, j - 1); } // 左
        }

        let mut count = 0;
        for i in 0..grid.len() {
            for j in 0..grid[i].len() {
                if grid[i][j] == '1' {
                    count += 1;
                    dfs(&mut grid, i, j);
                }
            }
        }
        count
    }
}
```
