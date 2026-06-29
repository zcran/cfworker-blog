---
title: "leetcode-并查集52"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 找出最安全路径

给你一个下标从 0 开始、大小为 n x n 的二维矩阵 grid ，其中 (r, c) 表示：

如果 grid[r][c] = 1 ，则表示一个存在小偷的单元格
如果 grid[r][c] = 0 ，则表示一个空单元格

你最开始位于单元格 (0, 0) 。在一步移动中，你可以移动到矩阵中的任一相邻单元格，包括存在小偷的单元格。

矩阵中路径的 安全系数 定义为：从路径中任一单元格到矩阵中任一小偷所在单元格的 最小 曼哈顿距离。

返回所有通向单元格 (n - 1, n - 1) 的路径中的 最大安全系数 。

单元格 (r, c) 的某个 相邻 单元格，是指在矩阵中存在的 (r, c + 1)、(r, c - 1)、(r + 1, c) 和 (r - 1, c) 之一。

两个单元格 (a, b) 和 (x, y) 之间的 曼哈顿距离 等于 | a - x | + | b - y | ，其中 |val| 表示 val 的绝对值。


```
use std::collections::{BinaryHeap, VecDeque};
use std::cmp::Reverse;

impl Solution {
    pub fn maximum_safeness_factor(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();

        // 起点或终点有小偷，无法安全到达
        if grid[0][0] == 1 || grid[n - 1][n - 1] == 1 {
            return 0;
        }

        // ===== 第一步：多源BFS计算每个格子到最近小偷的距离 =====
        let mut dist = vec![vec![-1; n]; n];
        let mut queue = VecDeque::new();
        let directions = [(0, 1), (0, -1), (1, 0), (-1, 0)];

        // 所有小偷作为BFS起点
        for i in 0..n {
            for j in 0..n {
                if grid[i][j] == 1 {
                    dist[i][j] = 0;
                    queue.push_back((i, j));
                }
            }
        }

        while let Some((x, y)) = queue.pop_front() {
            for (dx, dy) in directions {
                let nx = x as isize + dx;
                let ny = y as isize + dy;
                if nx >= 0 && nx < n as isize && ny >= 0 && ny < n as isize {
                    let (nx, ny) = (nx as usize, ny as usize);
                    if dist[nx][ny] == -1 {
                        dist[nx][ny] = dist[x][y] + 1;
                        queue.push_back((nx, ny));
                    }
                }
            }
        }

        // ===== 第二步：最大堆优先搜索（最大化路径上的最小距离） =====
        let mut visited = vec![vec![false; n]; n];
        // 堆存储：(当前路径的最小安全系数, 行, 列)
        // 使用 Reverse 让 BinaryHeap 成为最大堆（按安全系数降序）
        let mut heap = BinaryHeap::new();

        visited[0][0] = true;
        heap.push((dist[0][0], 0, 0));

        let mut answer = dist[0][0];

        while let Some((safeness, x, y)) = heap.pop() {
            // 更新答案：当前路径的最小安全系数
            answer = answer.min(safeness);

            // 到达终点，直接返回
            if x == n - 1 && y == n - 1 {
                return answer;
            }

            // 探索四个方向
            for (dx, dy) in directions {
                let nx = x as isize + dx;
                let ny = y as isize + dy;
                if nx >= 0 && nx < n as isize && ny >= 0 && ny < n as isize {
                    let (nx, ny) = (nx as usize, ny as usize);
                    if !visited[nx][ny] {
                        visited[nx][ny] = true;
                        heap.push((dist[nx][ny], nx, ny));
                    }
                }
            }
        }

        answer
    }
}
```
