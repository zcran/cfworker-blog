---
title: "leetcode-树16"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 给边赋权值的方案数 I

给你一棵 n 个节点的无向树，节点从 1 到 n 编号，树以节点 1 为根。树由一个长度为 n - 1 的二维整数数组 edges 表示，其中 edges[i] = [ui, vi] 表示在节点 ui 和 vi 之间有一条边。

一开始，所有边的权重为 0。你可以将每条边的权重设为 1 或 2。

两个节点 u 和 v 之间路径的 代价 是连接它们路径上所有边的权重之和。

选择任意一个 深度最大 的节点 x。返回从节点 1 到 x 的路径中，边权重之和为 奇数 的赋值方式数量。

由于答案可能很大，返回它对 10^9 + 7 取模的结果。

注意： 忽略从节点 1 到节点 x 的路径外的所有边。


```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    /// 快速幂
    #[inline]
    fn qpow(mut base: i64, mut exp: i64) -> i64 {
        let mut res = 1i64;
        while exp > 0 {
            if exp & 1 == 1 {
                res = res * base % Self::MOD;
            }
            base = base * base % Self::MOD;
            exp >>= 1;
        }
        res
    }

    /// 求树的最大深度（根为 1，深度从 0 开始计）
    /// 迭代 DFS，避免递归深度过大
    fn max_depth(graph: &[Vec<usize>]) -> i64 {
        let n = graph.len();
        let mut depth = vec![0usize; n];
        let mut visited = vec![false; n];
        let mut stack = vec![1usize];
        visited[1] = true;
        let mut max_dep = 0usize;

        while let Some(u) = stack.pop() {
            max_dep = max_dep.max(depth[u]);
            for &v in &graph[u] {
                if !visited[v] {
                    visited[v] = true;
                    depth[v] = depth[u] + 1;
                    stack.push(v);
                }
            }
        }

        max_dep as i64
    }

    /// 题意：
    /// - 所有边权可独立设为 1 或 2。
    /// - 选一个深度最大的节点 x，只关心根 1 到 x 的路径（长度为 max_dep 条边）。
    /// - 求该路径上边权和为奇数的赋值方式数量。
    ///
    /// 关键观察：
    /// 每条边权为 1 或 2，其中 1 是奇数，2 是偶数。
    /// 路径和奇偶性只取决于“选了多少条边权为 1 的边”的奇偶性。
    /// 设路径长度为 L = max_dep，则：
    /// - 总赋值方式：2^L
    /// - 奇数和的方式数 = 偶数和的方式数 = 2^{L-1}（当 L ≥ 1 时）
    /// 因此答案为 2^{L-1} mod (1e9+7)。
    pub fn assign_edge_weights(edges: Vec<Vec<i32>>) -> i32 {
        let n = edges.len() + 1;

        // 建邻接表（节点编号 1..n）
        let mut graph = vec![Vec::new(); n + 1];
        for e in &edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        let max_dep = Self::max_depth(&graph);

        // 若最大深度为 0（只有根节点），路径为空，和为 0（偶数），奇数方式数为 0
        if max_dep == 0 {
            return 0;
        }

        Self::qpow(2, max_dep - 1) as i32
    }
}
```
