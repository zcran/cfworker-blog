---
title: "leetcode-最短路21"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 最短路径中的边

给你一个 n 个节点的无向带权图，节点编号为 0 到 n - 1 。图中总共有 m 条边，用二维数组 edges 表示，其中 edges[i] = [ai, bi, wi] 表示节点 ai 和 bi 之间有一条边权为 wi 的边。

对于节点 0 为出发点，节点 n - 1 为结束点的所有最短路，你需要返回一个长度为 m 的 boolean 数组 answer ，如果 edges[i] 至少 在其中一条最短路上，那么 answer[i] 为 true ，否则 answer[i] 为 false 。

请你返回数组 answer 。

注意，图可能不连通。

```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 返回一个布尔数组，标记 edges 中的每条边是否位于从节点 0 到节点 n-1 的某条最短路径上。
    pub fn find_answer(n: i32, edges: Vec<Vec<i32>>) -> Vec<bool> {
        let n = n as usize;
        let edge_count = edges.len();

        // 构建邻接表：每个元素为 (邻居, 权重, 边索引)
        let mut graph = vec![vec![]; n];
        for (idx, edge) in edges.iter().enumerate() {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let w = edge[2] as i64;
            graph[u].push((v, w, idx));
            graph[v].push((u, w, idx));
        }

        // ---------- 1. Dijkstra 求从 0 到所有点的最短距离 ----------
        let mut dist = vec![i64::MAX; n];
        dist[0] = 0;
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, 0))); // (距离, 节点)

        while let Some(Reverse((d, u))) = heap.pop() {
            if d != dist[u] {
                continue;
            }
            for &(v, w, _) in &graph[u] {
                let new_d = d + w;
                if new_d < dist[v] {
                    dist[v] = new_d;
                    heap.push(Reverse((new_d, v)));
                }
            }
        }

        // 若终点不可达，直接返回全 false 的向量
        let mut ans = vec![false; edge_count];
        if dist[n - 1] == i64::MAX {
            return ans;
        }

        // ---------- 2. 反向遍历，标记所有位于最短路径上的边 ----------
        let mut visited = vec![false; n];
        let mut stack = vec![n - 1]; // 从终点开始DFS

        while let Some(y) = stack.pop() {
            if visited[y] {
                continue;
            }
            visited[y] = true;
            for &(x, w, edge_idx) in &graph[y] {
                // 若边 (x, y) 满足最短路径条件：从 0 到 x 的最短距离 + 边权 == 从 0 到 y 的最短距离
                if dist[x] + w == dist[y] {
                    ans[edge_idx] = true;
                    if !visited[x] {
                        stack.push(x);
                    }
                }
            }
        }

        ans
    }
}
```
