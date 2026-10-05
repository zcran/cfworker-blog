---
title: "leetcode-树13"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 移除边之后的权重最大和

存在一棵具有 n 个节点的无向树，节点编号为 0 到 n - 1。给你一个长度为 n - 1 的二维整数数组 edges，其中 edges[i] = [ui, vi, wi] 表示在树中节点 ui 和 vi 之间有一条权重为 wi 的边。

你的任务是移除零条或多条边，使得：

每个节点与至多 k 个其他节点有边直接相连，其中 k 是给定的输入。
剩余边的权重之和 最大化 。

返回在进行必要的移除后，剩余边的权重的 最大 可能和。


```
impl Solution {
    /// 树形 DP + 贪心
    ///
    /// 题意：删除若干边，使每个节点度数 ≤ k，且剩余边权和最大。
    ///
    /// 核心思路：
    /// 对每个节点 x，考虑它与父节点之间的边是否保留：
    /// - dp[x][0]：不保留 x 与父节点的边时，x 子树内的最大边权和（x 可用度数 ≤ k）
    /// - dp[x][1]：保留 x 与父节点的边时，x 子树内的最大边权和（x 可用度数 ≤ k-1，因为要留给父边 1 度）
    ///
    /// 对节点 x，先假设所有子边都不选，累加所有子节点的 dp[child][0]。
    /// 然后计算每条子边 (x, y, w) 的“增量”：
    ///     gain = dp[y][1] + w - dp[y][0]
    /// 若 gain > 0，说明保留这条子边能增加收益。
    /// 按 gain 从大到小排序，最多选 k 或 k-1 条（取决于 x 与父边是否保留）。
    ///
    /// 最后返回 dp[0][0]（根节点无父边，可用度数 ≤ k）。
    pub fn maximize_sum_of_weights(edges: Vec<Vec<i32>>, k: i32) -> i64 {
        let n = edges.len() + 1;
        let k = k as usize;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        let mut total_weight = 0i64;
        for e in &edges {
            let (x, y, w) = (e[0] as usize, e[1] as usize, e[2] as i64);
            graph[x].push((y, w));
            graph[y].push((x, w));
            total_weight += w;
        }

        // 优化：若所有节点度数都 ≤ k，则无需删边，直接返回总权重
        if graph.iter().all(|neighbors| neighbors.len() <= k) {
            return total_weight;
        }

        // 迭代后序遍历，避免递归深度过大
        // 第一遍：生成遍历顺序
        let mut order = Vec::with_capacity(n);
        let mut parent_edge = vec![(usize::MAX, 0i64); n]; // (父节点, 到父节点的边权)
        let mut visited = vec![false; n];
        let mut stack = vec![0usize];
        visited[0] = true;

        while let Some(u) = stack.pop() {
            order.push(u);
            for &(v, w) in &graph[u] {
                if !visited[v] {
                    visited[v] = true;
                    parent_edge[v] = (u, w);
                    stack.push(v);
                }
            }
        }

        // dp0[u]：不保留 u 与父节点的边时，u 子树最大边权和
        // dp1[u]：保留 u 与父节点的边时，u 子树最大边权和
        let mut dp0 = vec![0i64; n];
        let mut dp1 = vec![0i64; n];

        // 逆序处理（自底向上）
        for &u in order.iter().rev() {
            let mut base = 0i64; // 所有子边都不选时的收益
            let mut gains = Vec::new(); // 各子边的增量

            for &(v, w) in &graph[u] {
                if v == parent_edge[u].0 {
                    continue;
                }
                base += dp0[v];
                let gain = dp1[v] + w - dp0[v];
                if gain > 0 {
                    gains.push(gain);
                }
            }

            // 按增量从大到小排序
            gains.sort_unstable_by(|a, b| b.cmp(a));

            // dp1[u]：保留父边，可用子边数 ≤ k-1
            let mut sum1 = base;
            for i in 0..gains.len().min(k.saturating_sub(1)) {
                sum1 += gains[i];
            }
            dp1[u] = sum1;

            // dp0[u]：不保留父边，可用子边数 ≤ k
            let mut sum0 = base;
            for i in 0..gains.len().min(k) {
                sum0 += gains[i];
            }
            dp0[u] = sum0;
        }

        dp0[0]
    }
}
```
