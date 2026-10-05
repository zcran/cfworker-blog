---
title: "leetcode-广度优先搜索25"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 二进制矩阵中的最短路径

给你一个 n x n 的二进制矩阵 grid 中，返回矩阵中最短 畅通路径 的长度。如果不存在这样的路径，返回 -1 。

二进制矩阵中的 畅通路径 是一条从 左上角 单元格（即，(0, 0)）到 右下角 单元格（即，(n - 1, n - 1)）的路径，该路径同时满足下述要求：

路径途经的所有单元格的值都是 0 。

路径中所有相邻的单元格应当在 8 个方向之一 上连通（即，相邻两单元之间彼此不同且共享一条边或者一个角）。

畅通路径的长度 是该路径途经的单元格总数。


```
use std::collections::VecDeque;

impl Solution {
    pub fn shortest_path_binary_matrix(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();

        // 起点或终点被阻塞，直接无解
        if grid[0][0] == 1 || grid[n - 1][n - 1] == 1 {
            return -1;
        }

        // 原地修改 grid：0 表示可走，非 0 表示已访问，其值记录路径长度
        let mut grid = grid;
        grid[0][0] = 1;

        // 8 个方向：上下左右 + 四个对角
        const DIRS: [(i32, i32); 8] = [
            (-1, -1), (-1, 0), (-1, 1),
            (0, -1),           (0, 1),
            (1, -1),  (1, 0),  (1, 1),
        ];

        let mut queue = VecDeque::new();
        queue.push_back((0usize, 0usize));

        while let Some((x, y)) = queue.pop_front() {
            // 到达右下角，当前格子的值即为路径长度
            if x == n - 1 && y == n - 1 {
                return grid[x][y];
            }

            for &(dx, dy) in &DIRS {
                let nx = x as i32 + dx;
                let ny = y as i32 + dy;

                // 越界检查
                if nx < 0 || ny < 0 || nx >= n as i32 || ny >= n as i32 {
                    continue;
                }
                let (nx, ny) = (nx as usize, ny as usize);

                // 只走未访问过的 0 格子
                if grid[nx][ny] == 0 {
                    grid[nx][ny] = grid[x][y] + 1;
                    queue.push_back((nx, ny));
                }
            }
        }

        -1
    }
}
```
