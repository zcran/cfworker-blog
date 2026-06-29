---
title: "leetcode-并查集60"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 总价值可以被 K 整除的岛屿数目

给你一个 m x n 的矩阵 grid 和一个正整数 k。一个 岛屿 是由 正 整数（表示陆地）组成的，并且陆地间 四周 连通（水平或垂直）。

一个岛屿的总价值是该岛屿中所有单元格的值之和。

返回总价值可以被 k 整除 的岛屿数量。


```
impl Solution {
    pub fn count_islands(mut grid: Vec<Vec<i32>>, k: i32) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let mut result = 0;
        let directions = [(0, 1), (0, -1), (1, 0), (-1, 0)];

        // DFS 计算岛屿总价值并标记已访问
        fn dfs(grid: &mut Vec<Vec<i32>>, x: usize, y: usize, m: usize, n: usize) -> i64 {
            let mut sum = grid[x][y] as i64;
            grid[x][y] = 0; // 标记为已访问

            for (dx, dy) in [(0, 1), (0, -1), (1, 0), (-1, 0)] {
                let nx = x as isize + dx;
                let ny = y as isize + dy;
                if nx >= 0 && nx < m as isize && ny >= 0 && ny < n as isize {
                    let (nx, ny) = (nx as usize, ny as usize);
                    if grid[nx][ny] > 0 {
                        sum += dfs(grid, nx, ny, m, n);
                    }
                }
            }
            sum
        }

        // 遍历所有单元格
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] > 0 {
                    let total = dfs(&mut grid, i, j, m, n);
                    if total % k as i64 == 0 {
                        result += 1;
                    }
                }
            }
        }

        result
    }
}
```
