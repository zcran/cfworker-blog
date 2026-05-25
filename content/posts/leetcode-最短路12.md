---
title: "leetcode-最短路12"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 到达角落需要移除障碍物的最小数目

给你一个下标从 0 开始的二维整数数组 grid ，数组大小为 m x n 。每个单元格都是两个值之一：

0 表示一个 空 单元格，
1 表示一个可以移除的 障碍物 。
你可以向上、下、左、右移动，从一个空单元格移动到另一个空单元格。

现在你需要从左上角 (0, 0) 移动到右下角 (m - 1, n - 1) ，返回需要移除的障碍物的 最小 数目。

```
use std::collections::VecDeque;

impl Solution {
    /// 计算从网格左上角 (0,0) 到右下角 (row-1, col-1) 需要移除的最少障碍物数量。
    /// 每次移动到障碍物 (1) 代价为 1，移动到空格 (0) 代价为 0。
    /// 使用 0-1 BFS（双端队列）保证线性时间复杂度。
    pub fn minimum_obstacles(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();
        let target = (rows - 1, cols - 1);

        // 方向数组：用于四个方向的遍历 (上, 右, 下, 左)
        const DIRS: [(isize, isize); 4] = [(-1, 0), (0, 1), (1, 0), (0, -1)];

        // 距离数组，初始化为最大值（表示未访问）
        let mut dist = vec![vec![i32::MAX; cols]; rows];
        let mut deque = VecDeque::new();

        dist[0][0] = 0;
        deque.push_front((0, 0));

        while let Some((r, c)) = deque.pop_front() {
            let current_cost = dist[r][c];

            // 到达终点，直接返回（0-1 BFS 首次弹出即为最优）
            if (r, c) == target {
                return current_cost;
            }

            // 遍历四个方向
            for &(dr, dc) in &DIRS {
                let nr = r as isize + dr;
                let nc = c as isize + dc;

                // 边界检查
                if nr < 0 || nr >= rows as isize || nc < 0 || nc >= cols as isize {
                    continue;
                }
                let (nr, nc) = (nr as usize, nc as usize);

                // 该步的代价：如果下一步是障碍物则 +1，否则 +0
                let next_cost = current_cost + if grid[nr][nc] == 1 { 1 } else { 0 };

                // 如果找到更优路径，更新距离并相应入队
                if next_cost < dist[nr][nc] {
                    dist[nr][nc] = next_cost;
                    if grid[nr][nc] == 1 {
                        deque.push_back((nr, nc)); // 代价为 1，放队尾
                    } else {
                        deque.push_front((nr, nc)); // 代价为 0，放队首
                    }
                }
            }
        }

        // 根据题目保证，一定存在路径，但理论上如果不可达则返回 -1（此处 unreachable）
        unreachable!()
    }
}
```
