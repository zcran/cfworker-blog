---
title: "leetcode-拓扑排序15"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 有向无环图中一个节点的所有祖先

给你一个正整数 n ，它表示一个 有向无环图 中节点的数目，节点编号为 0 到 n - 1 （包括两者）。

给你一个二维整数数组 edges ，其中 edges[i] = [fromi, toi] 表示图中一条从 fromi 到 toi 的单向边。

请你返回一个数组 answer，其中 answer[i]是第 i 个节点的所有 祖先 ，这些祖先节点 升序 排序。

如果 u 通过一系列边，能够到达 v ，那么我们称节点 u 是节点 v 的 祖先 节点。

```
impl Solution {
    /// 返回每个节点的所有祖先节点（LeetCode 2192）。
    /// 给定 n 个节点（编号 0..n-1）和有向边 edges（每条边 u->v），
    /// 对于每个节点 v，返回所有能够到达 v 的节点 u（u ≠ v），结果按升序排列。
    pub fn get_ancestors(n: i32, edges: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let n = n as usize;
        // 建立反向图：reverse_graph[v] 存储所有直接指向 v 的节点（即 v 的父节点）
        let mut reverse_graph = vec![Vec::new(); n];
        for edge in &edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            reverse_graph[v].push(u); // 反向边：从 v 可以访问其祖先 u
        }

        // 深度优先搜索（DFS），从节点 x 开始，标记所有能到达的节点（即 x 的所有祖先）
        fn dfs(x: usize, graph: &[Vec<usize>], visited: &mut [bool]) {
            visited[x] = true;
            for &next in &graph[x] {
                if !visited[next] {
                    dfs(next, graph, visited);
                }
            }
        }

        let mut ancestors = vec![Vec::new(); n];
        let mut visited = vec![false; n];

        // 对每个节点作为起点进行 DFS，收集所有可达节点（但不包括自身）
        for i in 0..n {
            visited.fill(false);            // 重置访问标记
            dfs(i, &reverse_graph, &mut visited);
            visited[i] = false;             // 排除自身（也可在收集时过滤，但此处提前清除更高效）

            // 收集所有被标记的节点（即 i 的祖先），并转换为 i32
            ancestors[i] = visited
                .iter()
                .enumerate()
                .filter_map(|(node, &is_ancestor)| if is_ancestor { Some(node as i32) } else { None })
                .collect();
        }

        ancestors
    }
}
```
