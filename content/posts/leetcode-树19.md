---
title: "leetcode-树19"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 树中找到带权中位节点

给你一个整数 n，以及一棵 无向带权 树，根节点为节点 0，树中共有 n 个节点，编号从 0 到 n - 1。该树由一个长度为 n - 1 的二维数组 edges 表示，其中 edges[i] = [ui, vi, wi] 表示存在一条从节点 ui 到 vi 的边，权重为 wi。

带权中位节点 定义为从 ui 到 vi 路径上的 第一个 节点 x，使得从 ui 到 x 的边权之和 大于等于 该路径总权值和的一半。

给你一个二维整数数组 queries。对于每个 queries[j] = [uj, vj]，求出从 uj 到 vj 路径上的带权中位节点。

返回一个数组 ans，其中 ans[j] 表示查询 queries[j] 的带权中位节点编号。


```
impl Solution {
    /// 带权中位节点查询
    ///
    /// 核心思路：
    /// 1. 预处理倍增表 up[k][i]（节点 i 向上跳 2^k 步）、深度 depth、根到节点的距离 dist。
    /// 2. 对查询 (u, v)，先求 LCA，得到路径总权重 total。
    /// 3. 从 u 出发，在路径上找到第一个节点 x 使得 dist(u, x) * 2 >= total。
    ///    - 若 u 到 LCA 的距离 * 2 >= total，则 x 在 u -> LCA 段；
    ///    - 否则 x 在 LCA -> v 段，等价于从 v 出发找第一个使 dist(v, x) * 2 <= total 的节点。
    ///
    /// 倍增跳转时注意方向：从 u 向 LCA 跳时，dist(u, ancestor) 随跳数增加而增大；
    /// 从 v 向 LCA 跳时，dist(v, ancestor) 随跳数增加而增大，但条件是 <= total。
    pub fn find_median(n: i32, edges: Vec<Vec<i32>>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for e in &edges {
            let (u, v, w) = (e[0] as usize, e[1] as usize, e[2]);
            graph[u].push((v, w));
            graph[v].push((u, w));
        }

        // 迭代 DFS 预处理 depth、dist、parent[0]
        let mut parent = vec![0usize; n];
        let mut depth = vec![0usize; n];
        let mut dist = vec![0i64; n];
        let mut max_depth = 0usize;

        let mut visited = vec![false; n];
        let mut stack = vec![0usize];
        visited[0] = true;

        while let Some(u) = stack.pop() {
            max_depth = max_depth.max(depth[u]);
            for &(v, w) in &graph[u] {
                if !visited[v] {
                    visited[v] = true;
                    parent[v] = u;
                    depth[v] = depth[u] + 1;
                    dist[v] = dist[u] + w as i64;
                    stack.push(v);
                }
            }
        }

        // 倍增表
        let log = 64 - max_depth.leading_zeros() as usize;
        let mut up = vec![vec![0usize; n]; log];
        up[0].copy_from_slice(&parent);
        for k in 1..log {
            for i in 0..n {
                up[k][i] = up[k - 1][up[k - 1][i]];
            }
        }

        // LCA
        let lca = |mut u: usize, mut v: usize| -> usize {
            if depth[u] < depth[v] {
                std::mem::swap(&mut u, &mut v);
            }
            let diff = depth[u] - depth[v];
            for k in 0..log {
                if (diff >> k) & 1 == 1 {
                    u = up[k][u];
                }
            }
            if u == v {
                return u;
            }
            for k in (0..log).rev() {
                if up[k][u] != up[k][v] {
                    u = up[k][u];
                    v = up[k][v];
                }
            }
            up[0][u]
        };

        queries
            .iter()
            .map(|q| {
                let (u, v) = (q[0] as usize, q[1] as usize);
                if u == v {
                    return u as i32;
                }

                let w = lca(u, v);
                let total = dist[u] + dist[v] - 2 * dist[w];

                let u_to_lca = dist[u] - dist[w];
                if u_to_lca * 2 >= total {
                    // 中位节点在 u -> LCA 段
                    // 从 u 向上跳，找到最远的祖先 anc 使得 dist(u, anc) * 2 < total
                    // 然后答案是 anc 的父节点
                    let mut node = u;
                    for k in (0..log).rev() {
                        let anc = up[k][node];
                        let d = dist[u] - dist[anc];
                        if d * 2 < total {
                            node = anc;
                        }
                    }
                    up[0][node] as i32
                } else {
                    // 中位节点在 LCA -> v 段
                    // 从 v 向上跳，找到最远的祖先 anc 使得 dist(v, anc) * 2 <= total
                    // 然后答案是 anc 自身
                    let mut node = v;
                    for k in (0..log).rev() {
                        let anc = up[k][node];
                        let d = dist[v] - dist[anc];
                        if d * 2 <= total {
                            node = anc;
                        }
                    }
                    node as i32
                }
            })
            .collect()
    }
}
```
