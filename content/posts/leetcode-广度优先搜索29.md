---
title: "leetcode-广度优先搜索29"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 网格中的最短路径

给你一个 m * n 的网格，其中每个单元格不是 0（空）就是 1（障碍物）。每一步，您都可以在空白单元格中上、下、左、右移动。

如果您 最多 可以消除 k 个障碍物，请找出从左上角 (0, 0) 到右下角 (m-1, n-1) 的最短路径，并返回通过该路径所需的步数。如果找不到这样的路径，则返回 -1 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn shortest_path(grid: Vec<Vec<i32>>, k: i32) -> i32 {
        let (m, n) = (grid.len(), grid[0].len());

        // 每个状态记录 (row, col, 剩余可消除次数)
        // visited[row][col] 记录到达该格时用过的最大消除次数，
        // 若新路径剩余次数 <= 已记录的最佳，则剪枝（剩余越多越好）。
        let mut best = vec![vec![-1i32; n]; m];

        let start_remain = k - grid[0][0];
        if start_remain < 0 {
            return -1;
        }
        best[0][0] = start_remain;

        let mut queue = VecDeque::new();
        queue.push_back((0usize, 0usize, start_remain));

        let mut steps = 0;
        const DIRS: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        while !queue.is_empty() {
            // 逐层 BFS，每层代表一步
            for _ in 0..queue.len() {
                let (r, c, remain) = queue.pop_front().unwrap();

                if r == m - 1 && c == n - 1 {
                    return steps;
                }

                for &(dr, dc) in &DIRS {
                    let nr = r as isize + dr;
                    let nc = c as isize + dc;
                    if nr < 0 || nc < 0 || nr >= m as isize || nc >= n as isize {
                        continue;
                    }
                    let (nr, nc) = (nr as usize, nc as usize);

                    let next_remain = remain - grid[nr][nc];
                    if next_remain < 0 {
                        continue; // 障碍太多，走不通
                    }
                    // 只有当剩余次数比之前记录的更多时，才值得继续探索
                    if next_remain > best[nr][nc] {
                        best[nr][nc] = next_remain;
                        queue.push_back((nr, nc, next_remain));
                    }
                }
            }
            steps += 1;
        }

        -1
    }
}
```
