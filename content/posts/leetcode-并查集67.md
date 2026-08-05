---
title: "leetcode-并查集67"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 判断二分图

存在一个 无向图 ，图中有 n 个节点。其中每个节点都有一个介于 0 到 n - 1 之间的唯一编号。

给定一个二维数组 graph ，表示图，其中 graph[u] 是一个节点数组，由节点 u 的邻接节点组成。形式上，对于 graph[u] 中的每个 v ，都存在一条位于节点 u 和节点 v 之间的无向边。该无向图同时具有以下属性：

· 不存在自环（graph[u] 不包含 u）。
· 不存在平行边（graph[u] 不包含重复值）。
· 如果 v 在 graph[u] 内，那么 u 也应该在 graph[v] 内（该图是无向图）
· 这个图可能不是连通图，也就是说两个节点 u 和 v 之间可能不存在一条连通彼此的路径。

二分图 定义：如果能将一个图的节点集合分割成两个独立的子集 A 和 B ，并使图中的每一条边的两个节点一个来自 A 集合，一个来自 B 集合，就将这个图称为 二分图 。

如果图是二分图，返回 true ；否则，返回 false 。


```
use std::collections::VecDeque;

impl Solution {
    /// 判断图是否为二分图（BFS 染色法）
    pub fn is_bipartite(graph: Vec<Vec<i32>>) -> bool {
        let n = graph.len();
        let mut color = vec![0i8; n]; // 0=未染色，1和-1表示两种颜色

        for start in 0..n {
            if color[start] != 0 {
                continue; // 已染色，跳过
            }

            // BFS 从 start 开始染色
            let mut queue = VecDeque::new();
            queue.push_back(start);
            color[start] = 1;

            while let Some(u) = queue.pop_front() {
                let c = color[u];
                for &v in &graph[u] {
                    let v = v as usize;
                    if color[v] == 0 {
                        color[v] = -c;
                        queue.push_back(v);
                    } else if color[v] == c {
                        return false; // 相邻节点同色，非二分图
                    }
                    // 若为 -c 则继续
                }
            }
        }

        true
    }
}
```
