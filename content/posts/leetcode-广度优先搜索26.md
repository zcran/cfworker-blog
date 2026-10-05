---
title: "leetcode-广度优先搜索26"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 地图分析

你现在手里有一份大小为 n x n 的 网格 grid，上面的每个 单元格 都用 0 和 1 标记好了。其中 0 代表海洋，1 代表陆地。

请你找出一个海洋单元格，这个海洋单元格到离它最近的陆地单元格的距离是最大的，并返回该距离。如果网格上只有陆地或者海洋，请返回 -1。

我们这里说的距离是「曼哈顿距离」（ Manhattan Distance）：(x0, y0) 和 (x1, y1) 这两个单元格之间的距离是 |x0 - x1| + |y0 - y1| 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn max_distance(mut grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();

        // 多源 BFS：所有陆地入队作为起点
        let mut queue = VecDeque::new();
        for i in 0..n {
            for j in 0..n {
                if grid[i][j] == 1 {
                    queue.push_back((i as i32, j as i32));
                }
            }
        }

        // 全是海洋或全是陆地都无解
        if queue.is_empty() || queue.len() == n * n {
            return -1;
        }

        const DIRS: [(i32, i32); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];
        let mut distance = -1; // 初始 -1，第一层扩展后变成 0，即最近陆地的距离

        while !queue.is_empty() {
            distance += 1;
            // 逐层扩展，每层代表距离 +1
            for _ in 0..queue.len() {
                let (x, y) = queue.pop_front().unwrap();
                for &(dx, dy) in &DIRS {
                    let nx = x + dx;
                    let ny = y + dy;
                    // 越界或已访问（非 0）则跳过
                    if nx < 0 || ny < 0 || nx >= n as i32 || ny >= n as i32 {
                        continue;
                    }
                    let (nx, ny) = (nx as usize, ny as usize);
                    if grid[nx][ny] == 0 {
                        grid[nx][ny] = 1; // 标记为已访问
                        queue.push_back((nx as i32, ny as i32));
                    }
                }
            }
        }

        distance
    }
}
```
