---
title: "leetcode-并查集66"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 岛屿的最大面积

给定一个由 0 和 1 组成的非空二维数组 grid ，用来表示海洋岛屿地图。

一个 岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在水平或者竖直方向上相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着。

找到给定的二维数组中最大的岛屿面积。如果没有岛屿，则返回面积为 0 。


```
impl Solution {
    pub fn max_area_of_island(mut grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();
        let mut max_area = 0;

        /// DFS 搜索岛屿面积，并将已访问的土地置为 0 以避免重复。
        fn dfs(grid: &mut Vec<Vec<i32>>, i: usize, j: usize) -> i32 {
            const DIRS: [(i32, i32); 4] = [(0, 1), (0, -1), (1, 0), (-1, 0)];
            let mut area = 1;
            for &(dx, dy) in &DIRS {
                let ni = i as i32 + dx;
                let nj = j as i32 + dy;
                if ni >= 0 && ni < grid.len() as i32 && nj >= 0 && nj < grid[0].len() as i32 {
                    let ni = ni as usize;
                    let nj = nj as usize;
                    if grid[ni][nj] == 1 {
                        grid[ni][nj] = 0; // 标记已访问
                        area += dfs(grid, ni, nj);
                    }
                }
            }
            area
        }

        for i in 0..rows {
            for j in 0..cols {
                if grid[i][j] == 1 {
                    grid[i][j] = 0; // 标记起点
                    let area = dfs(&mut grid, i, j);
                    max_area = max_area.max(area);
                }
            }
        }
        max_area
    }
}
```
