---
title: "leetcode-图35"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 合并两棵树后的最小直径

给你两棵 无向 树，分别有 n 和 m 个节点，节点编号分别为 0 到 n - 1 和 0 到 m - 1 。给你两个二维整数数组 edges1 和 edges2 ，长度分别为 n - 1 和 m - 1 ，其中 edges1[i] = [ai, bi] 表示在第一棵树中节点 ai 和 bi 之间有一条边，edges2[i] = [ui, vi] 表示在第二棵树中节点 ui 和 vi 之间有一条边。

你必须在第一棵树和第二棵树中分别选一个节点，并用一条边连接它们。

请你返回添加边后得到的树中，最小直径 为多少。

一棵树的 直径 指的是树中任意两个节点之间的最长路径长度。


```
impl Solution {
    pub fn minimum_diameter_after_merge(edges1: Vec<Vec<i32>>, edges2: Vec<Vec<i32>>) -> i32 {
        let d1 = Self::tree_diameter(&edges1);
        let d2 = Self::tree_diameter(&edges2);
        // 合并后的最小直径 = max(两棵树原直径, 两棵树半径之和 + 1)
        // 树的半径 = ceil(直径 / 2) = (直径 + 1) / 2
        (d1).max(d2).max((d1 + 1) / 2 + (d2 + 1) / 2 + 1)
    }

    /// 计算树的直径
    fn tree_diameter(edges: &[Vec<i32>]) -> i32 {
        let n = edges.len() + 1;
        if n == 1 {
            return 0;
        }

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        let mut diameter = 0;
        Self::dfs(0, 0, &graph, &mut diameter);
        diameter
    }

    /// DFS 返回从当前节点出发的最长链长度
    /// 同时更新直径（经过当前节点的最长路径）
    fn dfs(node: usize, parent: usize, graph: &[Vec<usize>], diameter: &mut i32) -> i32 {
        let mut max_len = 0; // 当前节点的最长子链

        for &next in &graph[node] {
            if next != parent {
                let child_len = Self::dfs(next, node, graph, diameter) + 1;
                // 经过当前节点的最长路径 = 最长子链 + 次长子链
                *diameter = (*diameter).max(max_len + child_len);
                max_len = max_len.max(child_len);
            }
        }

        max_len
    }
}
```
