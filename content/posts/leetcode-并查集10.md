---
title: "leetcode-并查集10"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 水位上升的泳池中游泳


在一个 n x n 的整数矩阵 grid 中，每一个方格的值 grid[i][j] 表示位置 (i, j) 的平台高度。

当开始下雨时，在时间为 t 时，水池中的水位为 t 。你可以从一个平台游向四周相邻的任意一个平台，但是前提是此时水位必须同时淹没这两个平台。假定你可以瞬间移动无限距离，也就是默认在方格内部游动是不耗时的。当然，在你游泳的时候你必须待在坐标方格里面。

你从坐标方格的左上平台 (0，0) 出发。返回 你到达坐标方格的右下平台 (n-1, n-1) 所需的最少时间 。



```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    pub fn swim_in_water(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();
        let mut dist = vec![vec![i32::MAX; n]; n]; // 到达每个位置所需的最短时间
        let mut heap = BinaryHeap::new();

        // 起点：需要的时间至少为 grid[0][0]
        dist[0][0] = grid[0][0];
        heap.push(Reverse((grid[0][0], 0, 0)));

        // 四个方向：上、下、左、右
        const DIRS: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        while let Some(Reverse((time, x, y))) = heap.pop() {
            // 如果当前时间不是最优解，跳过
            if time != dist[x][y] {
                continue;
            }

            // 到达终点，直接返回
            if x == n - 1 && y == n - 1 {
                return time;
            }

            // 探索四个方向
            for &(dx, dy) in &DIRS {
                let nx = x as isize + dx;
                let ny = y as isize + dy;

                if nx >= 0 && nx < n as isize && ny >= 0 && ny < n as isize {
                    let nx = nx as usize;
                    let ny = ny as usize;
                    // 到达下一个平台需要的时间 = max(当前时间, 下一个平台高度)
                    let new_time = time.max(grid[nx][ny]);

                    // 如果找到了更优路径，更新并加入堆
                    if new_time < dist[nx][ny] {
                        dist[nx][ny] = new_time;
                        heap.push(Reverse((new_time, nx, ny)));
                    }
                }
            }
        }

        dist[n - 1][n - 1]
    }
}
```
