---
title: "leetcode-堆4"
date: 2026-08-21T09:54:10+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 接雨水 II

给你一个 m x n 的矩阵，其中的值均为非负整数，代表二维高度图每个单元的高度，请计算图中形状最多能接多少体积的雨水。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 优先队列 BFS（类似 Dijkstra）计算二维接雨水体积
    ///
    /// 思路：边界是"木桶的短板"，水无法从边界流出。从边界最低处向内扩展，
    /// 每个内部格子的接水量 = 当前边界最低高度 - 该格子高度（若为正）。
    /// 扩展时更新边界高度为 max(当前边界高度, 该格子高度)。
    pub fn trap_rain_water(mut height_map: Vec<Vec<i32>>) -> i32 {
        let m = height_map.len();
        let n = height_map[0].len();
        if m <= 2 || n <= 2 { return 0; }

        // Rust BinaryHeap 默认最大堆，用负数模拟最小堆
        let mut heap: BinaryHeap<(i32, usize, usize)> = BinaryHeap::new();

        // 将边界入堆并标记为已访问
        for i in [0, m - 1] {
            for j in 0..n {
                heap.push((-height_map[i][j], i, j));
                height_map[i][j] = -1;
            }
        }
        for j in [0, n - 1] {
            for i in 1..m - 1 {
                heap.push((-height_map[i][j], i, j));
                height_map[i][j] = -1;
            }
        }

        let mut ans = 0;
        let dirs = [(0, -1), (0, 1), (-1, 0), (1, 0)];

        while let Some((neg_h, i, j)) = heap.pop() {
            let wall = -neg_h; // 当前木桶的最低边界高度
            for (di, dj) in dirs {
                let x = (i as i32 + di) as usize;
                let y = (j as i32 + dj) as usize;
                if x < m && y < n && height_map[x][y] >= 0 {
                    ans += (wall - height_map[x][y]).max(0);
                    heap.push((-(wall.max(height_map[x][y])), x, y));
                    height_map[x][y] = -1;
                }
            }
        }

        ans
    }
}
```
