---
title: "leetcode-回溯36"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 不同路径 III


在二维网格 grid 上，有 4 种类型的方格：

1 表示起始方格。且只有一个起始方格。
2 表示结束方格，且只有一个结束方格。
0 表示我们可以走过的空方格。
-1 表示我们无法跨越的障碍。

返回在四个方向（上、下、左、右）上行走时，从起始方格到结束方格的不同路径的数目。

每一个无障碍方格都要通过一次，但是一条路径中不能重复通过同一个方格。

```
impl Solution {
    /// 返回从起点到终点且经过所有无障碍方格的路径数
    ///
    /// 思路：DFS 回溯，维护剩余待访问方格数，到达终点时检查是否全部访问
    pub fn unique_paths_iii(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();
        let mut grid = grid;

        let (mut start, mut end, mut remain) = (0, 0, 0);

        // 统计需要访问的方格数（0 和 2），记录起点和终点
        for i in 0..rows {
            for j in 0..cols {
                match grid[i][j] {
                    0 => remain += 1,
                    1 => {
                        start = i * cols + j;
                        remain += 1; // 起点也需要访问
                    }
                    2 => {
                        end = i * cols + j;
                        remain += 1; // 终点也需要访问
                    }
                    _ => {}
                }
            }
        }

        let mut visited = vec![vec![false; cols]; rows];

        /// DFS 搜索
        /// - pos: 当前位置（一维编码）
        /// - remain: 剩余需要访问的方格数（包括当前格）
        fn dfs(
            grid: &Vec<Vec<i32>>,
            visited: &mut Vec<Vec<bool>>,
            pos: usize,
            end: usize,
            remain: i32,
            rows: usize,
            cols: usize,
        ) -> i32 {
            let x = pos / cols;
            let y = pos % cols;

            // 标记当前格已访问
            visited[x][y] = true;

            // 到达终点：检查是否访问了所有方格
            if pos == end {
                let res = if remain == 1 { 1 } else { 0 };
                visited[x][y] = false;
                return res;
            }

            // 四个方向
            const DIRS: [(i32, i32); 4] = [(0, 1), (0, -1), (1, 0), (-1, 0)];
            let mut count = 0;

            for &(dx, dy) in &DIRS {
                let nx = x as i32 + dx;
                let ny = y as i32 + dy;

                // 边界检查
                if nx < 0 || nx >= rows as i32 || ny < 0 || ny >= cols as i32 {
                    continue;
                }

                let nx = nx as usize;
                let ny = ny as usize;

                // 未访问且不是障碍
                if !visited[nx][ny] && grid[nx][ny] != -1 {
                    count += dfs(grid, visited, nx * cols + ny, end, remain - 1, rows, cols);
                }
            }

            // 回溯
            visited[x][y] = false;
            count
        }

        dfs(&grid, &mut visited, start, end, remain, rows, cols)
    }
}
```
