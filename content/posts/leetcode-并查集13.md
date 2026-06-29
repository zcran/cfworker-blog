---
title: "leetcode-并查集13"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 最大人工岛

给你一个大小为 n x n 二进制矩阵 grid 。最多 只能将一格 0 变成 1 。

返回执行此操作后，grid 中最大的岛屿面积是多少？

岛屿 由一组上、下、左、右四个方向相连的 1 形成。


```
impl Solution {
    pub fn largest_island(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();
        let mut id = vec![vec![0; n]; n]; // 每个格子所属岛屿的编号（0 表示水）
        let mut sizes = vec![0]; // sizes[0] 占位，岛屿编号从 1 开始

        // DFS 标记岛屿并计算面积
        fn dfs(grid: &[Vec<i32>], id: &mut [Vec<usize>], i: usize, j: usize, idx: usize) -> i32 {
            id[i][j] = idx;
            let mut area = 1;
            let dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)];

            for &(di, dj) in &dirs {
                let ni = i as isize + di;
                let nj = j as isize + dj;
                if ni >= 0 && ni < grid.len() as isize && nj >= 0 && nj < grid.len() as isize {
                    let ni = ni as usize;
                    let nj = nj as usize;
                    if grid[ni][nj] == 1 && id[ni][nj] == 0 {
                        area += dfs(grid, id, ni, nj, idx);
                    }
                }
            }
            area
        }

        // 遍历所有格子，标记岛屿
        for i in 0..n {
            for j in 0..n {
                if grid[i][j] == 1 && id[i][j] == 0 {
                    let idx = sizes.len();
                    let area = dfs(&grid, &mut id, i, j, idx);
                    sizes.push(area);
                }
            }
        }

        // 如果全是水，返回 1（可以把一个水变成陆地）
        if sizes.len() == 1 {
            return 1;
        }

        let mut max_area = *sizes.iter().max().unwrap();

        // 尝试将每个 0 变成 1，计算连通后的面积
        for i in 0..n {
            for j in 0..n {
                if grid[i][j] == 0 {
                    let mut adjacent = Vec::new();
                    let dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)];

                    // 收集四个方向不同的岛屿编号
                    for &(di, dj) in &dirs {
                        let ni = i as isize + di;
                        let nj = j as isize + dj;
                        if ni >= 0 && ni < n as isize && nj >= 0 && nj < n as isize {
                            let ni = ni as usize;
                            let nj = nj as usize;
                            let island_id = id[ni][nj];
                            if island_id > 0 && !adjacent.contains(&island_id) {
                                adjacent.push(island_id);
                            }
                        }
                    }

                    // 计算将当前水变成陆地后的面积：1（本身）+ 相邻岛屿面积之和
                    let total = 1 + adjacent.iter().map(|&idx| sizes[idx]).sum::<i32>();
                    max_area = max_area.max(total);
                }
            }
        }

        max_area
    }
}
```
