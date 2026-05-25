---
title: "leetcode-最短路29"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 恢复网络路径


给你一个包含 n 个节点（编号从 0 到 n - 1）的有向无环图。图由长度为 m 的二维数组 edges 表示，其中 edges[i] = [ui, vi, costi] 表示从节点 ui 到节点 vi 的单向通信，恢复成本为 costi。

一些节点可能处于离线状态。给定一个布尔数组 online，其中 online[i] = true 表示节点 i 在线。节点 0 和 n - 1 始终在线。

从 0 到 n - 1 的路径如果满足以下条件，那么它是 有效 的：

路径上的所有中间节点都在线。
路径上所有边的总恢复成本不超过 k。
对于每条有效路径，其 分数 定义为该路径上的最小边成本。

返回所有有效路径中的 最大 路径分数（即最大 最小 边成本）。如果没有有效路径，则返回 -1。

```
impl Solution {
    /// 找到最大的阈值 `lower`，使得存在一条从 0 到 n-1 的路径，路径上每条边的权值 ≥ `lower`，
    /// 且路径权值总和 ≤ `k`。如果不存在满足条件的路径，返回 -1。
    ///
    /// # 参数
    /// - `edges`: 每条边为 [起点, 终点, 权值]（节点编号从 0 开始）
    /// - `online`: 节点是否在线（只能在两端都在线的边上行走）
    /// - `k`: 允许的最大路径总权值
    ///
    /// # 返回
    /// 满足条件的最大阈值，若没有可行路径则返回 -1。
    pub fn find_max_path_score(edges: Vec<Vec<i32>>, online: Vec<bool>, k: i64) -> i32 {
        let n = online.len();
        // 构建邻接表，只保留两端都在线的边
        let mut graph = vec![vec![]; n];
        let mut max_wt = -1;
        for e in edges {
            let x = e[0] as usize;
            let y = e[1] as usize;
            let wt = e[2] as i64;
            if online[x] && online[y] {
                graph[x].push((y, wt));
                if x == 0 {
                    max_wt = max_wt.max(wt);
                }
            }
        }

        // 二分查找：在 [ -1, max_wt ] 范围内寻找最大可行 lower
        let (mut left, mut right) = (-1, max_wt + 1);
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if Self::check(mid, &graph, n, k) {
                left = mid;
            } else {
                right = mid;
            }
        }
        left as i32
    }

    /// 检查给定的阈值 `lower` 是否可行：
    /// 是否存在一条从 0 到 n-1 的路径，边权 ≥ lower，且总路径和 ≤ k。
    fn check(lower: i64, graph: &Vec<Vec<(usize, i64)>>, n: usize, k: i64) -> bool {
        // memo[x] 表示从 x 到 n-1 的最小路径和，-1 表示未计算
        let mut memo = vec![-1; n];
        // 定义递归 DFS，返回从 x 到终点的最小路径和，要求路径上边权 ≥ lower。
        // 若无法到达，返回一个极大值（这里用 i64::MAX / 2 防止溢出）。
        fn dfs(x: usize, memo: &mut [i64], graph: &[Vec<(usize, i64)>], lower: i64) -> i64 {
            if x == memo.len() - 1 {
                return 0; // 到达终点
            }
            // 注意：这里只进行不可变读取，不产生可变借用
            if memo[x] != -1 {
                return memo[x];
            }
            let mut best = i64::MAX / 2;
            for &(y, wt) in &graph[x] {
                if wt >= lower {
                    let candidate = dfs(y, memo, graph, lower).saturating_add(wt);
                    best = best.min(candidate);
                }
            }
            memo[x] = best;
            best
        }
        let min_dist = dfs(0, &mut memo, graph, lower);
        min_dist <= k
    }
}
```
