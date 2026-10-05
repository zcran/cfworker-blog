---
title: "leetcode-广度优先搜索23"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 边界着色

给你一个大小为 m x n 的整数矩阵 grid ，表示一个网格。另给你三个整数 row、col 和 color 。网格中的每个值表示该位置处的网格块的颜色。

如果两个方块在任意 4 个方向上相邻，则称它们 相邻 。

如果两个方块具有相同的颜色且相邻，它们则属于同一个 连通分量 。

连通分量的边界 是指连通分量中满足下述条件之一的所有网格块：

在上、下、左、右任意一个方向上与不属于同一连通分量的网格块相邻
在网格的边界上（第一行/列或最后一行/列）

请你使用指定颜色 color 为所有包含网格块 grid[row][col] 的 连通分量的边界 进行着色。

并返回最终的网格 grid 。


```
impl Solution {
    pub fn color_border(mut grid: Vec<Vec<i32>>, row: i32, col: i32, color: i32) -> Vec<Vec<i32>> {
        const DIRS: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        let (rows, cols) = (grid.len(), grid[0].len());
        let (sr, sc) = (row as usize, col as usize);
        let original = grid[sr][sc];

        // 记录连通分量的边界坐标，最后统一染色，避免边搜索边染色影响判断
        let mut visited = vec![vec![false; cols]; rows];
        let mut borders = Vec::new();
        let mut stack = vec![(sr, sc)];
        visited[sr][sc] = true;

        while let Some((r, c)) = stack.pop() {
            let mut is_border = false;

            for &(dr, dc) in &DIRS {
                let nr = r as isize + dr;
                let nc = c as isize + dc;

                // 越界 => 边界
                if nr < 0 || nc < 0 || nr >= rows as isize || nc >= cols as isize {
                    is_border = true;
                    continue;
                }

                let (nr, nc) = (nr as usize, nc as usize);
                if grid[nr][nc] != original {
                    // 相邻不同色 => 边界
                    is_border = true;
                } else if !visited[nr][nc] {
                    visited[nr][nc] = true;
                    stack.push((nr, nc));
                }
            }

            if is_border {
                borders.push((r, c));
            }
        }

        for (r, c) in borders {
            grid[r][c] = color;
        }

        grid
    }
}
```
