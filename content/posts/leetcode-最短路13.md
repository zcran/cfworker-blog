---
title: "leetcode-最短路13"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 在网格图中访问一个格子的最少时间


给你一个 m x n 的矩阵 grid ，每个元素都为 非负 整数，其中 grid[row][col] 表示可以访问格子 (row, col) 的 最早 时间。也就是说当你访问格子 (row, col) 时，最少已经经过的时间为 grid[row][col] 。

你从 最左上角 出发，出发时刻为 0 ，你必须一直移动到上下左右相邻四个格子中的 任意 一个格子（即不能停留在格子上）。每次移动都需要花费 1 单位时间。

请你返回 最早 到达右下角格子的时间，如果你无法到达右下角的格子，请你返回 -1 。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 计算从 (0,0) 到 (m-1, n-1) 的最早到达时间。
    /// 每个格子 grid[i][j] 表示该格子最早可被访问的时间。
    /// 每步移动耗时 1，且到达时间必须满足奇偶性约束（与曼哈顿距离奇偶性一致）。
    /// 如果无法到达，返回 -1。
    pub fn minimum_time(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();

        // 四个方向：上、右、下、左（注意类型为 i32，便于边界计算）
        const DIRS: [[i32; 2]; 4] = [[-1, 0], [0, 1], [1, 0], [0, -1]];

        // 如果起始点的两个邻居都不可达（需要时间 >1 才能进入），则无法开始
        if (rows > 1 && grid[1][0] > 1) && (cols > 1 && grid[0][1] > 1) {
            return -1;
        }

        // 最短到达时间表，初始化为最大值
        let mut best_time = vec![vec![i32::MAX; cols]; rows];
        best_time[0][0] = 0;

        // 最小堆：存储 (当前时间, 行, 列)
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, 0, 0)));

        while let Some(Reverse((time, r, c))) = heap.pop() {
            // 到达终点，直接返回
            if (r, c) == (rows - 1, cols - 1) {
                return time;
            }

            // 遍历四个方向
            for dir in &DIRS {
                let nr = r as i32 + dir[0];
                let nc = c as i32 + dir[1];

                // 边界检查
                if nr < 0 || nr >= rows as i32 || nc < 0 || nc >= cols as i32 {
                    continue;
                }
                let (nr, nc) = (nr as usize, nc as usize);

                // 计算到达该格子的最早可能时间：
                // 1. 从当前时间+1 与 格子要求时间 中取最大值
                let mut next_time = time + 1;
                if next_time < grid[nr][nc] {
                    next_time = grid[nr][nc];
                }
                // 2. 奇偶性调整：使时间与 (nr+nc) 的奇偶性一致（因为从起点(0,0)开始，到达曼哈顿距离为 d 的格子时间必与 d 同奇偶）
                let parity_diff = (next_time - (nr + nc) as i32) & 1;
                next_time += parity_diff;

                // 如果找到了更优的时间，更新并入堆
                if next_time < best_time[nr][nc] {
                    best_time[nr][nc] = next_time;
                    heap.push(Reverse((next_time, nr, nc)));
                }
            }
        }

        // 理论上可到达，但若堆为空未返回终点，则不可达（按题意不会发生）
        -1
    }
}
```
