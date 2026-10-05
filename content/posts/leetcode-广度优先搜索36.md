---
title: "leetcode-广度优先搜索36"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 地图中的最高点

给你一个大小为 m x n 的整数矩阵 isWater ，它代表了一个由 陆地 和 水域 单元格组成的地图。

如果 isWater[i][j] == 0 ，格子 (i, j) 是一个 陆地 格子。
如果 isWater[i][j] == 1 ，格子 (i, j) 是一个 水域 格子。

你需要按照如下规则给每个单元格安排高度：

每个格子的高度都必须是非负的。
如果一个格子是 水域 ，那么它的高度必须为 0 。
任意相邻的格子高度差 至多 为 1 。当两个格子在正东、南、西、北方向上相互紧挨着，就称它们为相邻的格子。（也就是说它们有一条公共边）

找到一种安排高度的方案，使得矩阵中的最高高度值 最大 。

请你返回一个大小为 m x n 的整数矩阵 height ，其中 height[i][j] 是格子 (i, j) 的高度。如果有多种解法，请返回 任意一个 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn highest_peak(is_water: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let (m, n) = (is_water.len(), is_water[0].len());

        // 多源 BFS：所有水域作为起点，高度为 0；
        // 用 -1 表示陆地未访问，最终填成到最近水域的曼哈顿距离。
        let mut height = is_water;
        let mut queue = VecDeque::new();

        for i in 0..m {
            for j in 0..n {
                if height[i][j] == 1 {
                    height[i][j] = 0;
                    queue.push_back((i, j));
                } else {
                    height[i][j] = -1;
                }
            }
        }

        const DIRS: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        while let Some((x, y)) = queue.pop_front() {
            for &(dx, dy) in &DIRS {
                let nx = x as isize + dx;
                let ny = y as isize + dy;
                if nx < 0 || ny < 0 || nx >= m as isize || ny >= n as isize {
                    continue;
                }
                let (nx, ny) = (nx as usize, ny as usize);
                if height[nx][ny] == -1 {
                    height[nx][ny] = height[x][y] + 1;
                    queue.push_back((nx, ny));
                }
            }
        }

        height
    }
}
```
