---
title: "leetcode-模拟69"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 统计网格图中没有被保卫的格子数

给你两个整数 m 和 n 表示一个下标从 0 开始的 m x n 网格图。同时给你两个二维整数数组 guards 和 walls ，其中 guards[i] = [rowi, coli] 且 walls[j] = [rowj, colj] ，分别表示第 i 个警卫和第 j 座墙所在的位置。

一个警卫能看到 4 个坐标轴方向（即东、南、西、北）的 所有 格子，除非他们被一座墙或者另外一个警卫 挡住 了视线。如果一个格子能被 至少 一个警卫看到，那么我们说这个格子被 保卫 了。

请你返回空格子中，有多少个格子是 没被保卫 的。


```
impl Solution {
    /// 返回未被任何警卫看到的空格数量。
    /// 对每个警卫沿四个方向直线传播视线，遇到墙或另一个警卫停止。
    /// 用位掩码记录每个格子被哪些方向覆盖，避免重复传播。
    pub fn count_unguarded(m: i32, n: i32, guards: Vec<Vec<i32>>, walls: Vec<Vec<i32>>) -> i32 {
        let (m, n) = (m as usize, n as usize);
        // 0: 空格, -1: 警卫, -2: 墙, 正数: 被看到的位掩码
        let mut grid = vec![vec![0i32; n]; m];
        let dirs = [(1, 0), (0, 1), (-1, 0), (0, -1)];

        for g in &guards {
            grid[g[0] as usize][g[1] as usize] = -1;
        }
        for w in &walls {
            grid[w[0] as usize][w[1] as usize] = -2;
        }

        for g in &guards {
            let (x, y) = (g[0] as usize, g[1] as usize);
            for (k, &(dx, dy)) in dirs.iter().enumerate() {
                let (mut nx, mut ny) = (x as i32 + dx, y as i32 + dy);
                while nx >= 0 && nx < m as i32 && ny >= 0 && ny < n as i32 {
                    let (ix, iy) = (nx as usize, ny as usize);
                    if grid[ix][iy] < 0 { break; } // 遇到警卫或墙，视线阻断
                    grid[ix][iy] |= 1 << k;
                    nx += dx;
                    ny += dy;
                }
            }
        }

        grid.iter()
            .flat_map(|row| row.iter())
            .filter(|&&cell| cell == 0)
            .count() as i32
    }
}
```
