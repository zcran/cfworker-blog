---
title: "leetcode-并查集16"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 尽量减少恶意软件的传播

给出了一个由 n 个节点组成的网络，用 n × n 个邻接矩阵图 graph 表示。在节点网络中，当 graph[i][j] = 1 时，表示节点 i 能够直接连接到另一个节点 j。

一些节点 initial 最初被恶意软件感染。只要两个节点直接连接，且其中至少一个节点受到恶意软件的感染，那么两个节点都将被恶意软件感染。这种恶意软件的传播将继续，直到没有更多的节点可以被这种方式感染。

假设 M(initial) 是在恶意软件停止传播之后，整个网络中感染恶意软件的最终节点数。

如果从 initial 中移除某一节点能够最小化 M(initial)， 返回该节点。如果有多个节点满足条件，就返回索引最小的节点。

请注意，如果某个节点已从受感染节点的列表 initial 中删除，它以后仍有可能因恶意软件传播而受到感染。


```
use std::collections::{VecDeque, HashMap};

impl Solution {
    pub fn min_malware_spread(graph: Vec<Vec<i32>>, initial: Vec<i32>) -> i32 {
        let n = graph.len();

        // 1. 使用并查集标记所有连通分量
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
            if ra == rb { return; }
            if size[ra] < size[rb] {
                parent[ra] = rb;
                size[rb] += size[ra];
            } else {
                parent[rb] = ra;
                size[ra] += size[rb];
            }
        }

        // 合并所有相连的节点
        for i in 0..n {
            for j in i + 1..n {
                if graph[i][j] == 1 {
                    union(&mut parent, &mut size, i, j);
                }
            }
        }

        // 2. 统计每个连通分量中初始感染节点的数量
        let mut infected_count = HashMap::new();
        for &node in &initial {
            let root = find(&mut parent, node as usize);
            *infected_count.entry(root).or_insert(0) += 1;
        }

        // 3. 选择最优的移除节点
        let mut best_node = initial[0];
        let mut best_saved = 0;

        for &node in &initial {
            let root = find(&mut parent, node as usize);
            // 如果该连通分量只有一个初始感染节点，移除它可以拯救整个分量
            let saved = if infected_count.get(&root) == Some(&1) {
                size[root]
            } else {
                0
            };

            // 选择拯救节点数最多的；如果相同，选择编号最小的
            if saved > best_saved || (saved == best_saved && node < best_node) {
                best_saved = saved;
                best_node = node;
            }
        }

        best_node
    }
}
```
