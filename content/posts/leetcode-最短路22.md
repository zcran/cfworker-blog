---
title: "leetcode-最短路22"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 穿越网格图的安全路径


给你一个 m x n 的二进制矩形 grid 和一个整数 health 表示你的健康值。

你开始于矩形的左上角 (0, 0) ，你的目标是矩形的右下角 (m - 1, n - 1) 。

你可以在矩形中往上下左右相邻格子移动，但前提是你的健康值始终是 正数 。

对于格子 (i, j) ，如果 grid[i][j] = 1 ，那么这个格子视为 不安全 的，会使你的健康值减少 1 。

如果你可以到达最终的格子，请你返回 true ，否则返回 false 。

注意 ，当你在最终格子的时候，你的健康值也必须为 正数 。


```
use std::collections::VecDeque;

impl Solution {
    /// 判断是否存在从左上角到右下角的路径，使得剩余生命值始终大于0
    /// 网格中 1 表示障碍物（消耗1点生命），0 表示空地（无消耗）
    /// 起点和终点也会消耗生命值（如果对应格子为1）
    ///
    /// # 参数
    /// - `grid`: 二维网格，0或1
    /// - `health`: 初始生命值
    ///
    /// # 返回值
    /// - `true` 如果存在安全路径，否则 `false`
    pub fn find_safe_walk(grid: Vec<Vec<i32>>, health: i32) -> bool {
        // 方向数组：右，左，下，上
        const DIRS: [(i32, i32); 4] = [(0, 1), (0, -1), (1, 0), (-1, 0)];
        let m = grid.len();
        let n = grid[0].len();

        // 记录到达每个格子时能拥有的最大剩余生命值，初始化为 -1 表示未访问
        let mut vis = vec![vec![-1; n]; m];

        // 起点消耗生命值
        let start_h = health - grid[0][0];
        // 如果起点即是终点，直接判断剩余生命值是否大于0
        if m == 1 && n == 1 {
            return start_h > 0;
        }
        // 起点生命值不足，直接返回 false
        if start_h <= 0 {
            return false;
        }

        // 队列存储三元组 (行, 列, 当前剩余生命值)
        let mut queue = VecDeque::new();
        vis[0][0] = start_h;
        queue.push_back((0, 0, start_h));

        // BFS 主循环
        while let Some((i, j, h)) = queue.pop_front() {
            // 遍历四个方向
            for (dx, dy) in DIRS {
                let x = i as i32 + dx;
                let y = j as i32 + dy;
                // 边界检查
                if x >= 0 && x < m as i32 && y >= 0 && y < n as i32 {
                    let (x, y) = (x as usize, y as usize);
                    let new_h = h - grid[x][y];
                    // 剩余生命值必须大于0
                    if new_h > 0 {
                        // 到达终点则成功
                        if x == m - 1 && y == n - 1 {
                            return true;
                        }
                        // 只有当新的剩余生命值大于之前记录的该格子最大值时才更新并入队
                        if vis[x][y] < new_h {
                            vis[x][y] = new_h;
                            queue.push_back((x, y, new_h));
                        }
                    }
                }
            }
        }
        // 队列空且未到达终点
        false
    }
}
```
