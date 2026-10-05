---
title: "leetcode-广度优先搜索22"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 腐烂的橘子

在给定的 m x n 网格 grid 中，每个单元格可以有以下三个值之一：

值 0 代表空单元格；
值 1 代表新鲜橘子；
值 2 代表腐烂的橘子。

每分钟，腐烂的橘子 周围 4 个方向上相邻 的新鲜橘子都会腐烂。

返回 直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn oranges_rotting(grid: Vec<Vec<i32>>) -> i32 {
        let mut grid = grid;
        let (rows, cols) = (grid.len(), grid[0].len());

        // 多源 BFS：所有腐烂橘子作为第 0 层
        let mut queue = VecDeque::new();
        let mut fresh = 0; // 新鲜橘子数量，用于提前判断是否可能全部腐烂
        for r in 0..rows {
            for c in 0..cols {
                match grid[r][c] {
                    2 => queue.push_back((r, c)),
                    1 => fresh += 1,
                    _ => {}
                }
            }
        }

        if fresh == 0 {
            return 0;
        }

        const DIRS: [(i32, i32); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];
        let mut minutes = 0;

        // 逐层扩展，每层代表一分钟
        while !queue.is_empty() && fresh > 0 {
            minutes += 1;
            for _ in 0..queue.len() {
                let (r, c) = queue.pop_front().unwrap();
                for &(dr, dc) in &DIRS {
                    let nr = r as i32 + dr;
                    let nc = c as i32 + dc;
                    if nr < 0 || nc < 0 || nr >= rows as i32 || nc >= cols as i32 {
                        continue;
                    }
                    let (nr, nc) = (nr as usize, nc as usize);
                    if grid[nr][nc] == 1 {
                        grid[nr][nc] = 2;
                        fresh -= 1;
                        queue.push_back((nr, nc));
                    }
                }
            }
        }

        if fresh == 0 { minutes } else { -1 }
    }
}
```
