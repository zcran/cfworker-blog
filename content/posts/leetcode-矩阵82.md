---
title: "leetcode-矩阵82"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 至多 K 次转向的最小路径代价

给你一个大小为 m x n 的二维整数数组 grid，其中 grid[i][j] 表示访问单元格 (i, j) 的代价，另给你一个整数 k。

你从 左上角 单元格 (0, 0) 出发，目标是到达 右下角 单元格 (m - 1, n - 1)。

在每个单元格中，你可以向四个方向之一移动一步：上、下、左 或 右。

路径的代价是所访问的所有单元格的值之和，包括 起始单元格和目标单元格。如果一个单元格被多次访问，其值每次被访问时都会计入。

返回在 至多 进行 k 次转向的情况下，到达 (m - 1, n - 1) 的 最小 可能路径代价。如果不存在这样的路径，返回 -1。

当两次连续移动之间的方向发生改变时，就发生了一次 转向 。例如，先向右移动再向下移动算作一次转向，而连续向右移动则不算转向。


```
impl Solution {
    pub fn min_cost(grid: Vec<Vec<i32>>, k0: i32) -> i32 {
        const INF: i32 = i32::MAX / 2;
        let m = grid.len();
        let n = grid[0].len();
        let k0 = k0 as usize;

        // 四个方向的状态数组：到达 (i,j) 且最后一步方向为 下/上/右/左 的最小代价
        // 使用 Vec<Vec<i32>> 而非固定大小数组，便于按需分配
        let mut d = vec![vec![INF; n]; m];
        let mut u = vec![vec![INF; n]; m];
        let mut r = vec![vec![INF; n]; m];
        let mut l = vec![vec![INF; n]; m];

        // 起点初始化
        d[0][0] = grid[0][0];
        r[0][0] = grid[0][0];

        let mut ans = INF;

        // 每轮循环允许最多 k 次转向：k=0 表示不转向，k=1 表示最多 1 次转向，依此类推
        for _ in 0..=k0 {
            // 原地转向：四个方向状态取最小值，等价于允许在该格改变方向
            for i in 0..m {
                for j in 0..n {
                    let mn = d[i][j].min(u[i][j]).min(r[i][j]).min(l[i][j]);
                    d[i][j] = mn;
                    u[i][j] = mn;
                    r[i][j] = mn;
                    l[i][j] = mn;
                }
            }

            // 向下传播
            for i in 1..m {
                for j in 0..n {
                    d[i][j] = d[i][j].min(d[i - 1][j] + grid[i][j]);
                }
            }

            // 向上传播
            for i in (0..m - 1).rev() {
                for j in 0..n {
                    u[i][j] = u[i][j].min(u[i + 1][j] + grid[i][j]);
                }
            }

            // 向右传播
            for i in 0..m {
                for j in 1..n {
                    r[i][j] = r[i][j].min(r[i][j - 1] + grid[i][j]);
                }
            }

            // 向左传播
            for i in 0..m {
                for j in (0..n - 1).rev() {
                    l[i][j] = l[i][j].min(l[i][j + 1] + grid[i][j]);
                }
            }

            // 到达终点时，最后一步可以是向下或向右
            ans = ans.min(d[m - 1][n - 1]).min(r[m - 1][n - 1]);
        }

        if ans < INF { ans } else { -1 }
    }
}
```
