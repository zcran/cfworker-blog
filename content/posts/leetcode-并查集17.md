---
title: "leetcode-并查集17"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 尽量减少恶意软件的传播 II

给定一个由 n 个节点组成的网络，用 n x n 个邻接矩阵 graph 表示。在节点网络中，只有当 graph[i][j] = 1 时，节点 i 能够直接连接到另一个节点 j。

一些节点 initial 最初被恶意软件感染。只要两个节点直接连接，且其中至少一个节点受到恶意软件的感染，那么两个节点都将被恶意软件感染。这种恶意软件的传播将继续，直到没有更多的节点可以被这种方式感染。

假设 M(initial) 是在恶意软件停止传播之后，整个网络中感染恶意软件的最终节点数。

我们可以从 initial 中 删除一个节点，并完全移除该节点以及从该节点到任何其他节点的任何连接。

请返回移除后能够使 M(initial) 最小化的节点。如果有多个节点满足条件，返回索引 最小的节点 。


```
use std::collections::HashSet;

impl Solution {
    pub fn min_malware_spread(graph: Vec<Vec<i32>>, initial: Vec<i32>) -> i32 {
        let n = graph.len();
        let mut initial_set = vec![false; n];
        for &node in &initial {
            initial_set[node as usize] = true;
        }

        // 使用并查集合并所有非初始感染节点
        let mut parent = (0..n).collect::<Vec<_>>();
        let mut size = vec![1; n];

        fn find(parent: &mut [usize], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        fn union(parent: &mut [usize], size: &mut [usize], a: usize, b: usize) {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra == rb {
                return;
            }
            if size[ra] < size[rb] {
                parent[ra] = rb;
                size[rb] += size[ra];
            } else {
                parent[rb] = ra;
                size[ra] += size[rb];
            }
        }

        // 只合并非初始感染节点之间的边
        for i in 0..n {
            if initial_set[i] {
                continue;
            }
            for j in i + 1..n {
                if !initial_set[j] && graph[i][j] == 1 {
                    union(&mut parent, &mut size, i, j);
                }
            }
        }

        // 统计每个连通分量的大小（仅非初始感染节点）
        let mut component_size = vec![0; n];
        for i in 0..n {
            if !initial_set[i] {
                let root = find(&mut parent, i);
                component_size[root] = size[root];
            }
        }

        // 统计每个连通分量相邻的初始感染节点数量
        let mut infected_adj = vec![0; n];
        for &node in &initial {
            let node = node as usize;
            let mut visited = HashSet::new();
            for neighbor in 0..n {
                if !initial_set[neighbor] && graph[node][neighbor] == 1 {
                    let root = find(&mut parent, neighbor);
                    if visited.insert(root) {
                        infected_adj[root] += 1;
                    }
                }
            }
        }

        // 选择最优的移除节点
        let mut best_node = initial[0];
        let mut best_saved = 0;

        for &node in &initial {
            let node = node as usize;
            let mut saved = 0;
            let mut visited = HashSet::new();

            for neighbor in 0..n {
                if !initial_set[neighbor] && graph[node][neighbor] == 1 {
                    let root = find(&mut parent, neighbor);
                    if visited.insert(root) && infected_adj[root] == 1 {
                        saved += component_size[root];
                    }
                }
            }

            // 选择拯救节点最多的；如果相同，选择编号最小的
            if saved > best_saved || (saved == best_saved && node < best_node as usize) {
                best_saved = saved;
                best_node = node as i32;
            }
        }

        best_node
    }
}
```
