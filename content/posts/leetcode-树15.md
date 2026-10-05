---
title: "leetcode-树15"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 包含要求路径的最小带权子图 II

给你一个 无向带权 树，共有 n 个节点，编号从 0 到 n - 1。这棵树由一个二维整数数组 edges 表示，长度为 n - 1，其中 edges[i] = [ui, vi, wi] 表示存在一条连接节点 ui 和 vi 的边，权重为 wi。

此外，给你一个二维整数数组 queries，其中 queries[j] = [src1j, src2j, destj]。

返回一个长度等于 queries.length 的数组 answer，其中 answer[j] 表示一个子树的 最小总权重 ，使用该子树的边可以从 src1j 和 src2j 到达 destj 。

这里的 子树 是指原树中任意节点和边组成的连通子集形成的一棵有效树。


```
impl Solution {
    /// 题意：对每个查询 (a, b, c)，求包含 a、b、c 的最小子树（即三条路径的并）的总边权。
    /// 结论：答案 = (dist(a,b) + dist(b,c) + dist(a,c)) / 2。
    ///
    /// 实现：倍增法 LCA + 前缀距离，O((n + q) log n)。
    pub fn minimum_weight(edges: Vec<Vec<i32>>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = edges.len() + 1;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for e in &edges {
            let (u, v, w) = (e[0] as usize, e[1] as usize, e[2] as i64);
            graph[u].push((v, w));
            graph[v].push((u, w));
        }

        // 倍增层数
        let log = 64 - n.leading_zeros() as usize;

        // parent[k][i]：节点 i 向上跳 2^k 步到达的节点
        // depth[i]：节点深度
        // dist[i]：根到节点 i 的距离
        let mut parent = vec![vec![0usize; n]; log];
        let mut depth = vec![0usize; n];
        let mut dist = vec![0i64; n];

        // 迭代 DFS 建立父子关系、深度、距离
        let mut stack = vec![0usize];
        let mut visited = vec![false; n];
        visited[0] = true;
        parent[0][0] = 0;

        while let Some(u) = stack.pop() {
            for &(v, w) in &graph[u] {
                if !visited[v] {
                    visited[v] = true;
                    parent[0][v] = u;
                    depth[v] = depth[u] + 1;
                    dist[v] = dist[u] + w;
                    stack.push(v);
                }
            }
        }

        // 构建倍增表
        for k in 1..log {
            for i in 0..n {
                parent[k][i] = parent[k - 1][parent[k - 1][i]];
            }
        }

        // LCA
        let lca = |mut u: usize, mut v: usize| -> usize {
            if depth[u] < depth[v] {
                std::mem::swap(&mut u, &mut v);
            }
            // 将 u 跳到与 v 同深度
            let mut diff = depth[u] - depth[v];
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

        // 两点距离
        let distance = |u: usize, v: usize| -> i64 {
            let w = lca(u, v);
            dist[u] + dist[v] - 2 * dist[w]
        };

        queries
            .iter()
            .map(|q| {
                let (a, b, c) = (q[0] as usize, q[1] as usize, q[2] as usize);
                // 三条路径长度之和的一半即为最小子树的边权和
                ((distance(a, b) + distance(b, c) + distance(a, c)) / 2) as i32
            })
            .collect()
    }
}
```
