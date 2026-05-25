---
title: "leetcode-最短路11"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 包含要求路径的最小带权子图

给你一个整数 n ，它表示一个 带权有向 图的节点数，节点编号为 0 到 n - 1 。

同时给你一个二维整数数组 edges ，其中 edges[i] = [fromi, toi, weighti] ，表示从 fromi 到 toi 有一条边权为 weighti 的 有向 边。

最后，给你三个 互不相同 的整数 src1 ，src2 和 dest ，表示图中三个不同的点。

请你从图中选出一个 边权和最小 的子图，使得从 src1 和 src2 出发，在这个子图中，都 可以 到达 dest 。如果这样的子图不存在，请返回 -1 。

子图 中的点和边都应该属于原图的一部分。子图的边权和定义为它所包含的所有边的权值之和。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;


impl Solution {
    /// 计算从 src1 和 src2 分别到某个中间节点 v，再从 v 到 dest 的三段最短路径之和的最小值。
    /// 如果不存在这样的中间节点，返回 -1。
    pub fn minimum_weight(n: i32, edges: Vec<Vec<i32>>, src1: i32, src2: i32, dest: i32) -> i64 {
        let n = n as usize;
        let src1 = src1 as usize;
        let src2 = src2 as usize;
        let dest = dest as usize;

        // 正向图和反向图（邻接表）
        let mut graph = vec![vec![]; n];
        let mut graph_rev = vec![vec![]; n];
        for edge in edges {
            let x = edge[0] as usize;
            let y = edge[1] as usize;
            let w = edge[2] as i64;
            graph[x].push((y, w));
            graph_rev[y].push((x, w));
        }

        // Dijkstra 闭包，返回从 start 出发到所有节点的最短距离（-1 表示不可达）
        let dijkstra = |graph: &[Vec<(usize, i64)>], start: usize| -> Vec<i64> {
            let mut dist = vec![-1; n];
            dist[start] = 0;
            let mut heap = BinaryHeap::new();
            heap.push(Reverse((0, start)));

            while let Some(Reverse((d, u))) = heap.pop() {
                // 如果当前弹出的距离不是最新记录的最短距离，则跳过
                if dist[u] != -1 && d != dist[u] {
                    continue;
                }
                for &(v, w) in &graph[u] {
                    let new_d = d + w;
                    if dist[v] == -1 || new_d < dist[v] {
                        dist[v] = new_d;
                        heap.push(Reverse((new_d, v)));
                    }
                }
            }
            dist
        };

        // 三次 Dijkstra
        let dist_from_src1 = dijkstra(&graph, src1);
        let dist_from_src2 = dijkstra(&graph, src2);
        let dist_to_dest = dijkstra(&graph_rev, dest); // 反向图上从 dest 出发等价于原图所有点到 dest

        // 函数式求最小值
        (0..n)
            .filter_map(|i| {
                let d1 = dist_from_src1[i];
                let d2 = dist_from_src2[i];
                let d3 = dist_to_dest[i];
                if d1 != -1 && d2 != -1 && d3 != -1 {
                    Some(d1 + d2 + d3)
                } else {
                    None
                }
            })
            .min()
            .unwrap_or(-1)
    }
}

```
