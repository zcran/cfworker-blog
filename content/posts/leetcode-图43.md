---
title: "leetcode-图43"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## K 条边路径的最大边权和

给你一个整数 n 和一个包含 n 个节点（编号从 0 到 n - 1）的 有向无环图（DAG）。该图由二维数组 edges 表示，其中 edges[i] = [ui, vi, wi] 表示一条从节点 ui 到 vi 的有向边，边的权值为 wi。

同时给你两个整数 k 和 t。

你的任务是确定在图中边权和 尽可能大的 路径，该路径需满足以下两个条件：

路径包含 恰好 k 条边；

路径上的边权值之和 严格小于 t。

返回满足条件的一个路径的 最大 边权和。如果不存在这样的路径，则返回 -1。




```
impl Solution {
    /// 在 DAG 中寻找包含恰好 k 条边、边权和 < t 的最大路径和
    ///
    /// # 参数
    /// - `n`: 节点数
    /// - `edges`: 有向边 [u, v, w]
    /// - `k`: 路径必须包含的边数
    /// - `t`: 边权和必须严格小于 t
    ///
    /// # 返回
    /// 满足条件的最大边权和，若不存在则返回 -1
    pub fn max_weight(n: i32, edges: Vec<Vec<i32>>, k: i32, t: i32) -> i32 {
        let n = n as usize;
        let k = k as usize;

        // 如果路径所需边数超过节点数减 1，则不可能存在（无环，路径最长 n-1 条边）
        if n <= k {
            return -1;
        }

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let w = edge[2];
            graph[u].push((v, w));
        }

        let mut ans = -1;
        // 状态缓存：防止重复搜索相同状态 (x, step, sum)
        // 使用 HashSet 存储 (x, step, sum) 的组合，避免指数爆炸
        use std::collections::HashSet;
        let mut visited = HashSet::new();

        // 深度优先搜索
        // x: 当前节点, step: 已走边数, sum: 当前边权和
        fn dfs(
            x: usize,
            step: usize,
            sum: i32,
            k: usize,
            t: i32,
            graph: &[Vec<(usize, i32)>],
            ans: &mut i32,
            visited: &mut HashSet<(usize, usize, i32)>,
        ) {
            // 如果已走 k 条边，更新答案
            if step == k {
                *ans = (*ans).max(sum);
                return;
            }

            // 状态去重：每个 (x, step, sum) 只需探索一次
            if !visited.insert((x, step, sum)) {
                return;
            }

            // 遍历所有后继
            for &(y, w) in &graph[x] {
                let new_sum = sum + w;
                // 剪枝：边权和必须严格小于 t
                if new_sum < t {
                    dfs(y, step + 1, new_sum, k, t, graph, ans, visited);
                }
            }
        }

        // 枚举所有起点
        for start in 0..n {
            dfs(start, 0, 0, k, t, &graph, &mut ans, &mut visited);
        }

        ans
    }
}
```
