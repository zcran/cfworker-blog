---
title: "leetcode-并查集36"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 统计子岛屿

给你两个 m x n 的二进制矩阵 grid1 和 grid2 ，它们只包含 0 （表示水域）和 1 （表示陆地）。一个 岛屿 是由 四个方向 （水平或者竖直）上相邻的 1 组成的区域。任何矩阵以外的区域都视为水域。

如果 grid2 的一个岛屿，被 grid1 的一个岛屿 完全 包含，也就是说 grid2 中该岛屿的每一个格子都被 grid1 中同一个岛屿完全包含，那么我们称 grid2 中的这个岛屿为 子岛屿 。

请你返回 grid2 中 子岛屿 的 数目 。


```
impl Solution {
    pub fn count_sub_islands(grid1: Vec<Vec<i32>>, grid2: Vec<Vec<i32>>) -> i32 {
        let mut grid2 = grid2;                     // 可修改，用于标记已访问的陆地
        let rows = grid2.len();
        let cols = grid2[0].len();
        let directions = [(-1, 0), (1, 0), (0, -1), (0, 1)];
        let mut sub_islands = 0;

        for i in 0..rows {
            for j in 0..cols {
                if grid2[i][j] == 1 {
                    // 发现 grid2 的一个新岛屿，用栈进行 DFS 遍历
                    let mut stack = vec![(i, j)];
                    grid2[i][j] = 0;               // 标记为已访问
                    let mut is_sub = true;

                    while let Some((x, y)) = stack.pop() {
                        // 如果 grid1 对应位置是水域，则该岛屿不是子岛屿
                        if grid1[x][y] == 0 {
                            is_sub = false;
                        }

                        for &(dx, dy) in &directions {
                            let nx = x as i32 + dx;
                            let ny = y as i32 + dy;
                            if nx >= 0 && nx < rows as i32 && ny >= 0 && ny < cols as i32 {
                                let nx = nx as usize;
                                let ny = ny as usize;
                                if grid2[nx][ny] == 1 {
                                    grid2[nx][ny] = 0;
                                    stack.push((nx, ny));
                                }
                            }
                        }
                    }

                    if is_sub {
                        sub_islands += 1;
                    }
                }
            }
        }

        sub_islands
    }
}
```
