---
title: "leetcode-树17"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 给边赋权值的方案数 II

给你一棵有 n 个节点的无向树，节点从 1 到 n 编号，树以节点 1 为根。树由一个长度为 n - 1 的二维整数数组 edges 表示，其中 edges[i] = [ui, vi] 表示在节点 ui 和 vi 之间有一条边。

一开始，所有边的权重为 0。你可以将每条边的权重设为 1 或 2。

两个节点 u 和 v 之间路径的 代价 是连接它们路径上所有边的权重之和。

给定一个二维整数数组 queries。对于每个 queries[i] = [ui, vi]，计算从节点 ui 到 vi 的路径中，使得路径代价为 奇数 的权重分配方式数量。

返回一个数组 answer，其中 answer[i] 表示第 i 个查询的合法赋值方式数量。

由于答案可能很大，请对每个 answer[i] 取模 109 + 7。

注意： 对于每个查询，仅考虑 ui 到 vi 路径上的边，忽略其他边。


```
use std::collections::VecDeque;

const MOD: i64 = 1_000_000_007;

impl Solution {
    /// 题意：
    /// - 树中每条边可赋权 1 或 2。
    /// - 对查询 (u, v)，只考虑 u 到 v 路径上的边，求路径总权为奇数的赋值方式数。
    ///
    /// 关键观察：
    /// 路径长度为 L（边数）。每条边有 2 种选择，总共 2^L 种。
    /// 其中总权为奇数的方式数 = 总权为偶数的方式数 = 2^{L-1}（L ≥ 1）。
    /// 因此只需对每个查询求出 u、v 之间的距离（边数），答案即为 2^{L-1}。
    ///
    /// 实现：倍增法 LCA，O((n + q) log n)。
    pub fn assign_edge_weights(edges: Vec<Vec<i32>>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = edges.len() + 1;
        let log = 64 - n.leading_zeros() as usize;

        // 建邻接表（节点编号 1..=n）
        let mut graph = vec![Vec::new(); n + 1];
        for e in &edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // 预处理 2 的幂，避免重复计算
        let max_dist = n; // 最大距离不超过 n-1
        let mut pow2 = vec![1i64; max_dist + 1];
        for i in 1..=max_dist {
            pow2[i] = pow2[i - 1] * 2 % MOD;
        }

        // parent[k][i]：节点 i 向上跳 2^k 步到达的节点
        // depth[i]：节点深度（根 1 深度为 0）
        let mut parent = vec![vec![0usize; n + 1]; log];
        let mut depth = vec![0usize; n + 1];

        // 迭代 DFS 建立父子关系与深度
        let mut visited = vec![false; n + 1];
        let mut stack = vec![1usize];
        visited[1] = true;
        parent[0][1] = 0; // 根的父节点设为 0（哨兵）

        while let Some(u) = stack.pop() {
            for &v in &graph[u] {
                if !visited[v] {
                    visited[v] = true;
                    parent[0][v] = u;
                    depth[v] = depth[u] + 1;
                    stack.push(v);
                }
            }
        }

        // 构建倍增表
        for k in 1..log {
            for i in 1..=n {
                parent[k][i] = parent[k - 1][parent[k - 1][i]];
            }
        }

        // LCA 查询
        let lca = |mut u: usize, mut v: usize| -> usize {
            if depth[u] < depth[v] {
                std::mem::swap(&mut u, &mut v);
            }
            // 将 u 跳到与 v 同深度
            let diff = depth[u] - depth[v];
            for k in 0..log {
                if (diff >> k) & 1 == 1 {
                    u = parent[k][u];
                }
            }
            if u == v {
                return u;
            }
            // 同时向上跳
            for k in (0..log).rev() {
                if parent[k][u] != parent[k][v] {
                    u = parent[k][u];
                    v = parent[k][v];
                }
            }
            parent[0][u]
        };

        // 两点间距离（边数）
        let distance = |u: usize, v: usize| -> usize {
            let w = lca(u, v);
            depth[u] + depth[v] - 2 * depth[w]
        };

        queries
            .iter()
            .map(|q| {
                let (u, v) = (q[0] as usize, q[1] as usize);
                if u == v {
                    // 路径为空，代价为 0（偶数），奇数方式数为 0
                    0
                } else {
                    let d = distance(u, v);
                    pow2[d - 1] as i32
                }
            })
            .collect()
    }
}
```
