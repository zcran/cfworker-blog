---
title: "leetcode-堆71"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 交替方向的最小路径代价 III

给你两个整数 m 和 n，表示一个网格的行数和列数。你的目标是到达单元格 (m - 1, n - 1)。同时给你一个二维整数数组 penalty。

进入单元格 (i, j) 的代价为 (i + 1) * (j + 1)。

你从单元格 (0, 0) 开始，最初需要支付其入口代价。进入 (0, 0) 后执行的行动从 1 开始编号。

在每次行动中，你可以移动到一个 相邻 的单元格，或者在当前单元格等待。如果满足以下条件，则移动遵循奇偶性规则：

· 在 奇数编号 的行动中，你向 右 或向 下 移动。
· 在 偶数编号 的行动中，你向 左 或向 上 移动。

行动的代价由以下方式决定：

· 如果你遵循奇偶性规则移动，只需支付目标单元格的入口代价。
· 如果你在 违反 奇偶性规则的方向上移动，支付目标单元格的入口代价加上 penalty[i][j]，其中 (i, j) 是你移动前所在的单元格。
· 如果你在单元格 (i, j) 中等待，支付 penalty[i][j]。

在每次移动或等待之后，行动编号增加 1。因此，无论是否支付了惩罚代价，所需遵循的奇偶性规则在每次行动后都会交替改变。

返回到达 (m - 1, n - 1) 所需的 最小 总代价。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 到达 (m-1, n-1) 的最小总代价。
    ///
    /// 状态设计：
    /// - dis[i][j][k] 表示到达 (i,j) 且**下一次**行动编号的奇偶性为 k（0=偶, 1=奇）的最小代价。
    /// - 初始进入 (0,0) 消耗 1，之后第一次行动编号为 1（奇数），故初始状态 k=1。
    ///
    /// 奇偶性规则：
    /// - 奇数行动：只能向右 (0,1) 或向下 (1,0)
    /// - 偶数行动：只能向左 (0,-1) 或向上 (-1,0)
    /// - 违反规则移动需额外支付当前格子的 penalty
    /// - 原地等待支付 penalty，且行动编号奇偶性翻转
    pub fn min_cost(m: i32, n: i32, penalty: Vec<Vec<i32>>) -> i64 {
        let m = m as usize;
        let n = n as usize;

        // 预计算每个格子的入口代价 entry[i][j] = (i+1)*(j+1)
        // 用一维数组展平，缓存友好
        let mut entry = vec![0i64; m * n];
        for i in 0..m {
            let base = i * n;
            let row_val = (i + 1) as i64;
            for j in 0..n {
                entry[base + j] = row_val * (j + 1) as i64;
            }
        }

        // 四个方向：[左, 右, 上, 下]
        // idx%2: 0->左/上(偶数行动), 1->右/下(奇数行动)
        const DIRS: [(isize, isize); 4] = [(0, -1), (0, 1), (-1, 0), (1, 0)];

        // dis[i][j][k] 展平为一维，k∈{0,1}
        const INF: i64 = i64::MAX / 4;
        let mut dis = vec![INF; m * n * 2];

        // 优先队列: (代价, i, j, k)
        // 用 Reverse 实现小根堆
        let mut pq: BinaryHeap<Reverse<(i64, usize, usize, usize)>> = BinaryHeap::new();

        // 初始：支付 (0,0) 的入口代价 1，下一步行动编号为 1（奇数，k=1）
        dis[1] = 1; // (0,0,1) 的展平索引
        pq.push(Reverse((1, 0, 0, 1)));

        while let Some(Reverse((d, i, j, k))) = pq.pop() {
            // Dijkstra 终止条件：首次弹出终点即最优解
            if i == m - 1 && j == n - 1 {
                return d;
            }

            let idx = (i * n + j) * 2 + k;
            if d > dis[idx] {
                continue; // 过期状态
            }

            let p = penalty[i][j] as i64;

            // 1. 原地等待：支付 penalty，奇偶性翻转
            let wait_cost = d + p;
            let wait_idx = idx ^ 1; // k^1 翻转奇偶性
            if wait_cost < dis[wait_idx] {
                dis[wait_idx] = wait_cost;
                pq.push(Reverse((wait_cost, i, j, k ^ 1)));
            }

            // 2. 向四个方向移动
            for (dir_idx, &(di, dj)) in DIRS.iter().enumerate() {
                let ni = i as isize + di;
                let nj = j as isize + dj;
                if ni < 0 || ni >= m as isize || nj < 0 || nj >= n as isize {
                    continue;
                }
                let ni = ni as usize;
                let nj = nj as usize;

                // 奇偶性匹配判断：
                // dir_idx%2 == 1 表示右/下（奇数行动允许）
                // dir_idx%2 == 0 表示左/上（偶数行动允许）
                // 若 dir_idx%2 != k，则违反规则，需加 penalty
                let violation = (dir_idx & 1) ^ k;
                let move_cost = d + entry[ni * n + nj] + if violation == 1 { p } else { 0 };

                let next_idx = (ni * n + nj) * 2 + (k ^ 1);
                if move_cost < dis[next_idx] {
                    dis[next_idx] = move_cost;
                    pq.push(Reverse((move_cost, ni, nj, k ^ 1)));
                }
            }
        }

        // 不可达（理论上网格内始终可达）
        -1
    }
}
```
