---
title: "leetcode-图37"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 新增道路查询后的最短距离 I

给你一个整数 n 和一个二维整数数组 queries。

有 n 个城市，编号从 0 到 n - 1。初始时，每个城市 i 都有一条单向道路通往城市 i + 1（ 0 <= i < n - 1）。

queries[i] = [ui, vi] 表示新建一条从城市 ui 到城市 vi 的单向道路。每次查询后，你需要找到从城市 0 到城市 n - 1 的最短路径的长度。

返回一个数组 answer，对于范围 [0, queries.length - 1] 中的每个 i，answer[i] 是处理完前 i + 1 个查询后，从城市 0 到城市 n - 1 的最短路径的长度。


```
use std::collections::VecDeque;

impl Solution {
    pub fn shortest_distance_after_queries(n: i32, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;

        // 邻接表，每个节点指向后续节点
        let mut graph = vec![Vec::new(); n];
        for i in 0..n - 1 {
            graph[i].push(i + 1);
        }

        let mut ans = Vec::with_capacity(queries.len());

        for query in queries {
            let (u, v) = (query[0] as usize, query[1] as usize);
            graph[u].push(v);
            ans.push(Self::bfs(n, &graph));
        }

        ans
    }

    /// BFS 求从 0 到 n-1 的最短距离
    fn bfs(n: usize, graph: &[Vec<usize>]) -> i32 {
        let mut dist = vec![-1; n];
        let mut queue = VecDeque::with_capacity(n);

        dist[0] = 0;
        queue.push_back(0);

        while let Some(node) = queue.pop_front() {
            let next_dist = dist[node] + 1;

            for &next in &graph[node] {
                if dist[next] == -1 {
                    // 如果到达终点，可以提前返回（因为 BFS 保证首次到达即为最短）
                    if next == n - 1 {
                        return next_dist;
                    }
                    dist[next] = next_dist;
                    queue.push_back(next);
                }
            }
        }

        dist[n - 1]
    }
}
```
