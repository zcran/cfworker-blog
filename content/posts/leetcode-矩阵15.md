---
title: "leetcode-矩阵15"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 保持城市天际线

给你一座由 n x n 个街区组成的城市，每个街区都包含一座立方体建筑。给你一个下标从 0 开始的 n x n 整数矩阵 grid ，其中 grid[r][c] 表示坐落于 r 行 c 列的建筑物的 高度 。

城市的 天际线 是从远处观察城市时，所有建筑物形成的外部轮廓。从东、南、西、北四个主要方向观测到的 天际线 可能不同。

我们被允许为 任意数量的建筑物 的高度增加 任意增量（不同建筑物的增量可能不同） 。 高度为 0 的建筑物的高度也可以增加。然而，增加的建筑物高度 不能影响 从任何主要方向观察城市得到的 天际线 。

在 不改变 从任何主要方向观测到的城市 天际线 的前提下，返回建筑物可以增加的 最大高度增量总和 。


```
impl Solution {
    pub fn max_increase_keeping_skyline(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();

        // row_max[i]: 第 i 行的最大值（即从东西方向看的天际线）
        // col_max[j]: 第 j 列的最大值（即从南北方向看的天际线）
        let mut row_max = vec![0; n];
        let mut col_max = vec![0; n];

        for i in 0..n {
            for j in 0..n {
                let h = grid[i][j];
                row_max[i] = row_max[i].max(h);
                col_max[j] = col_max[j].max(h);
            }
        }

        // 每个建筑最高只能增加到 min(row_max[i], col_max[j])，
        // 否则会改变某个方向的天际线。累加所有可增加的增量。
        let mut ans = 0;
        for i in 0..n {
            for j in 0..n {
                ans += row_max[i].min(col_max[j]) - grid[i][j];
            }
        }

        ans
    }
}
```
