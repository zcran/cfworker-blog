---
title: "leetcode-图31"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 可以到达每一个节点的最少边反转次数

给你一个 n 个点的 简单有向图 （没有重复边的有向图），节点编号为 0 到 n - 1 。如果这些边是双向边，那么这个图形成一棵 树 。

给你一个整数 n 和一个 二维 整数数组 edges ，其中 edges[i] = [ui, vi] 表示从节点 ui 到节点 vi 有一条 有向边 。

边反转 指的是将一条边的方向反转，也就是说一条从节点 ui 到节点 vi 的边会变为一条从节点 vi 到节点 ui 的边。

对于范围 [0, n - 1] 中的每一个节点 i ，你的任务是分别 独立 计算 最少 需要多少次 边反转 ，从节点 i 出发经过 一系列有向边 ，可以到达所有的节点。

请你返回一个长度为 n 的整数数组 answer ，其中 answer[i]表示从节点 i 出发，可以到达所有节点的 最少边反转 次数。




```
impl Solution {
    pub fn min_edge_reversals(n: i32, edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;

        // 构建邻接表：(邻居节点, 方向权重)
        // 权重 1: 表示从当前节点到邻居是正向边（不需要反转）
        // 权重 -1: 表示从当前节点到邻居是反向边（需要反转）
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push((v, 1));   // u -> v 正向
            graph[v].push((u, -1));  // v -> u 需要反转
        }

        let mut ans = vec![0; n];

        // 第一次 DFS：计算从根节点 0 出发需要的反转次数
        Self::dfs(0, 0, &graph, &mut ans);

        // 第二次 DFS：换根 DP，计算所有节点的答案
        Self::reroot(0, 0, &graph, &mut ans);

        ans
    }

    /// 计算根节点 0 的答案：统计所有指向父节点的边（即需要反转的边）
    fn dfs(node: usize, parent: usize, graph: &[Vec<(usize, i32)>], ans: &mut [i32]) {
        for &(next, weight) in &graph[node] {
            if next != parent {
                // weight == -1 表示从 node 到 next 是反向边，需要反转
                if weight == -1 {
                    ans[0] += 1;
                }
                Self::dfs(next, node, graph, ans);
            }
        }
    }

    /// 换根 DP：根据父节点答案计算子节点答案
    fn reroot(node: usize, parent: usize, graph: &[Vec<(usize, i32)>], ans: &mut [i32]) {
        for &(next, weight) in &graph[node] {
            if next != parent {
                // 从 node 换根到 next：
                // - 如果 weight == 1 (node -> next 正向)，换根后这条边变为反向，需要多一次反转
                // - 如果 weight == -1 (next -> node 正向)，换根后这条边变为正向，少一次反转
                // 所以变化量 = weight (1 或 -1)
                ans[next] = ans[node] + weight;
                Self::reroot(next, node, graph, ans);
            }
        }
    }
}
```
