---
title: "leetcode-最短路6"
date: 2026-04-22T20:45:19+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 使网格图至少有一条有效路径的最小代价

给你一个 m x n 的网格图 grid 。 grid 中每个格子都有一个数字，对应着从该格子出发下一步走的方向。 grid[i][j] 中的数字可能为以下几种情况：

1 ，下一步往右走，也就是你会从 grid[i][j] 走到 grid[i][j + 1]
2 ，下一步往左走，也就是你会从 grid[i][j] 走到 grid[i][j - 1]
3 ，下一步往下走，也就是你会从 grid[i][j] 走到 grid[i + 1][j]
4 ，下一步往上走，也就是你会从 grid[i][j] 走到 grid[i - 1][j]
注意网格图中可能会有 无效数字 ，因为它们可能指向 grid 以外的区域。

一开始，你会从最左上角的格子 (0,0) 出发。我们定义一条 有效路径 为从格子 (0,0) 出发，每一步都顺着数字对应方向走，最终在最右下角的格子 (m - 1, n - 1) 结束的路径。有效路径 不需要是最短路径 。

你可以花费 cost = 1 的代价修改一个格子中的数字，但每个格子中的数字 只能修改一次 。

请你返回让网格图至少有一条有效路径的最小代价。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 计算从网格左上角 (0,0) 到右下角 (rows-1, cols-1) 的最小修改次数。
    /// 每个格子包含一个方向标识：1=右，2=左，3=下，4=上。
    /// 沿着当前方向移动不需要代价，改变方向需要代价 1。
    /// 使用 Dijkstra 算法，边权为 0 或 1。
    pub fn min_cost(grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();

        // 方向数组：每个条目 (方向值, 行变化, 列变化)
        // 方向值取自 grid 中的数字：1->右，2->左，3->下，4->上
        const DIRS: [(i32, i32, i32); 4] = [(1, 0, 1), (2, 0, -1), (3, 1, 0), (4, -1, 0)];

        // dist[i][j] = 从起点到 (i,j) 的最小修改次数
        let mut dist = vec![vec![i32::MAX; cols]; rows];
        dist[0][0] = 0;

        // 优先队列（最小堆），存储 (当前代价, 行, 列)
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, 0, 0)));

        // 可选：标记节点是否已从堆中弹出（用于加速，避免重复处理）
        let mut processed = vec![vec![false; cols]; rows];

        while let Some(Reverse((cost, r, c))) = heap.pop() {
            // 跳过已经处理过的节点（过时条目）
            if processed[r][c] {
                continue;
            }
            processed[r][c] = true;

            // 第一次弹出终点时即得到最短路径
            if r == rows - 1 && c == cols - 1 {
                return dist[r][c];
            }

            // 遍历四个方向
            for &(dir_val, dr, dc) in &DIRS {
                let nr = r as i32 + dr;
                let nc = c as i32 + dc;
                // 检查边界
                if nr < 0 || nr >= rows as i32 || nc < 0 || nc >= cols as i32 {
                    continue;
                }
                let nr = nr as usize;
                let nc = nc as usize;

                // 移动成本：与当前格子指示的方向一致则为 0，否则为 1
                let add_cost = if grid[r][c] == dir_val { 0 } else { 1 };
                let new_cost = cost + add_cost;

                if new_cost < dist[nr][nc] {
                    dist[nr][nc] = new_cost;
                    heap.push(Reverse((new_cost, nr, nc)));
                }
            }
        }

        // 如果堆耗尽仍未返回，返回终点的距离（理论上一定可达）
        dist[rows - 1][cols - 1]
    }
}
```
