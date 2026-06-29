---
title: "leetcode-并查集44"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 受限条件下可到达节点的数目

现有一棵由 n 个节点组成的无向树，节点编号从 0 到 n - 1 ，共有 n - 1 条边。

给你一个二维整数数组 edges ，长度为 n - 1 ，其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条边。另给你一个整数数组 restricted 表示 受限 节点。

在不访问受限节点的前提下，返回你可以从节点 0 到达的 最多 节点数目。

注意，节点 0 不 会标记为受限节点。


```
use std::collections::HashSet;

impl Solution {
    pub fn reachable_nodes(n: i32, edges: Vec<Vec<i32>>, restricted: Vec<i32>) -> i32 {
        let n = n as usize;

        // 构建邻接表（包含所有边）
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // 受限节点集合
        let restricted: HashSet<_> = restricted.into_iter().collect();

        // DFS 从节点 0 开始遍历，跳过受限节点
        let mut visited = vec![false; n];
        let mut stack = vec![0];
        visited[0] = true;
        let mut count = 0;

        while let Some(u) = stack.pop() {
            count += 1;
            for &v in &graph[u] {
                if !visited[v] && !restricted.contains(&(v as i32)) {
                    visited[v] = true;
                    stack.push(v);
                }
            }
        }

        count
    }
}
```
