---
title: "leetcode-最短路8"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---



## 从第一个节点出发到最后一个节点的受限路径数


现有一个加权无向连通图。给你一个正整数 n ，表示图中有 n 个节点，并按从 1 到 n 给节点编号；另给你一个数组 edges ，其中每个 edges[i] = [ui, vi, weighti] 表示存在一条位于节点 ui 和 vi 之间的边，这条边的权重为 weighti 。

从节点 start 出发到节点 end 的路径是一个形如 [z0, z1, z2, ..., zk] 的节点序列，满足 z0 = start 、zk = end 且在所有符合 0 <= i <= k-1 的节点 zi 和 zi+1 之间存在一条边。

路径的距离定义为这条路径上所有边的权重总和。用 distanceToLastNode(x) 表示节点 n 和 x 之间路径的最短距离。受限路径 为满足 distanceToLastNode(zi) > distanceToLastNode(zi+1) 的一条路径，其中 0 <= i <= k-1 。

返回从节点 1 出发到节点 n 的 受限路径数 。由于数字可能很大，请返回对 109 + 7 取余 的结果。


```
use std::{cmp::Reverse, collections::BinaryHeap};

impl Solution {
    /// 计算从节点 1 到节点 n 的“受限路径”数量。
    /// 受限路径：路径上相邻节点必须满足 `dist[u] > dist[v]`，
    /// 其中 `dist[x]` 表示节点 x 到节点 n 的最短距离（按边的权重求和）。
    pub fn count_restricted_paths(n: i32, edges: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;
        let target = n - 1; // 节点 n 的 0‑based 索引

        // ---------- 1. 构建无向加权图（邻接表）----------
        let mut graph = vec![vec![]; n];
        for edge in &edges {
            let u = (edge[0] - 1) as usize;
            let v = (edge[1] - 1) as usize;
            let w = edge[2] as usize;
            graph[u].push((v, w));
            graph[v].push((u, w));
        }

        // ---------- 2. Dijkstra 计算从目标节点 n 到所有节点的最短距离 ----------
        let mut shortest_dist = vec![usize::MAX; n];
        shortest_dist[target] = 0;
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, target))); // (距离, 节点)

        while let Some(Reverse((dist_u, u))) = heap.pop() {
            // 过时条目跳过
            if dist_u != shortest_dist[u] {
                continue;
            }
            for &(v, w) in &graph[u] {
                let new_dist = dist_u + w;
                if new_dist < shortest_dist[v] {
                    shortest_dist[v] = new_dist;
                    heap.push(Reverse((new_dist, v)));
                }
            }
        }

        // ---------- 3. 构建“受限”有向无环图 (DAG) ----------
        // 只保留满足 dist[u] > dist[v] 的边 u -> v。
        let mut dag = vec![vec![]; n];
        for u in 0..n {
            for &(v, _) in &graph[u] {
                if shortest_dist[u] > shortest_dist[v] {
                    dag[u].push(v);
                }
            }
        }

        // ---------- 4. 在 DAG 上记忆化搜索从节点 0（即原节点 1）到 target 的路径数 ----------
        const MOD: usize = 1_000_000_007;
        let mut dp = vec![None; n]; // dp[u] = 从 u 到 target 的受限路径数，None 表示未计算

        fn dfs(
            u: usize,
            target: usize,
            dag: &[Vec<usize>],
            dp: &mut Vec<Option<usize>>,
            mod_val: usize,
        ) -> usize {
            if let Some(cached) = dp[u] {
                return cached;
            }
            if u == target {
                return 1; // 到达目标节点本身，一条有效路径
            }
            let mut total = 0;
            for &v in &dag[u] {
                total = (total + dfs(v, target, dag, dp, mod_val)) % mod_val;
            }
            dp[u] = Some(total);
            total
        }

        dfs(0, target, &dag, &mut dp, MOD) as i32
    }
}
```
