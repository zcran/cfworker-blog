---
title: "leetcode-广度优先搜索33"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 收集树上所有苹果的最少时间

给你一棵有 n 个节点的无向树，节点编号为 0 到 n-1 ，它们中有一些节点有苹果。通过树上的一条边，需要花费 1 秒钟。你从 节点 0 出发，请你返回最少需要多少秒，可以收集到所有苹果，并回到节点 0 。

无向树的边由 edges 给出，其中 edges[i] = [fromi, toi] ，表示有一条边连接 from 和 toi 。除此以外，还有一个布尔数组 hasApple ，其中 hasApple[i] = true 代表节点 i 有一个苹果，否则，节点 i 没有苹果。


```
impl Solution {
    pub fn min_time(n: i32, edges: Vec<Vec<i32>>, has_apple: Vec<bool>) -> i32 {
        // 构建邻接表
        let n = n as usize;
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // 自底向上 DFS：返回以 node 为根的子树中，收集所有苹果并回到 node 所需的边数
        // 若子树中没有苹果且 node 本身也没有苹果，返回 0（不需要走这条边）
        fn dfs(node: usize, parent: usize, graph: &Vec<Vec<usize>>, has_apple: &Vec<bool>) -> i32 {
            let mut total = 0;
            for &next in &graph[node] {
                if next == parent {
                    continue;
                }
                let child = dfs(next, node, graph, has_apple);
                // 子树里有苹果（child > 0）或者子节点自身有苹果，则这条边需要来回走两趟
                if child > 0 || has_apple[next] {
                    total += child + 2;
                }
            }
            total
        }

        dfs(0, usize::MAX, &graph, &has_apple)
    }
}
```
