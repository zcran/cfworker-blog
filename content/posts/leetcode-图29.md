---
title: "leetcode-图29"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 图中的最短环

现有一个含 n 个顶点的 双向 图，每个顶点按从 0 到 n - 1 标记。图中的边由二维整数数组 edges 表示，其中 edges[i] = [ui, vi] 表示顶点 ui 和 vi 之间存在一条边。每对顶点最多通过一条边连接，并且不存在与自身相连的顶点。

返回图中 最短 环的长度。如果不存在环，则返回 -1 。

环 是指以同一节点开始和结束，并且路径中的每条边仅使用一次。


```
use std::collections::VecDeque;

impl Solution {
    pub fn find_shortest_cycle(n: i32, edges: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        let mut ans = i32::MAX;

        // 枚举每个节点作为起点，BFS 找最小环
        for start in 0..n {
            let mut dist = vec![-1; n];
            let mut parent = vec![n; n];  // 记录父节点，n 表示无父节点
            dist[start] = 0;

            let mut queue = VecDeque::new();
            queue.push_back(start);

            while let Some(u) = queue.pop_front() {
                for &v in &graph[u] {
                    if dist[v] == -1 {
                        // 未访问：记录距离和父节点
                        dist[v] = dist[u] + 1;
                        parent[v] = u;
                        queue.push_back(v);
                    } else if parent[u] != v {
                        // 已访问且不是父节点，发现环
                        // 环的长度 = dist[u] + dist[v] + 1
                        let cycle_len = dist[u] + dist[v] + 1;
                        ans = ans.min(cycle_len);
                    }
                }
            }
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```
