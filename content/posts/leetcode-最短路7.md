---
title: "leetcode-最短路7"
date: 2026-04-22T20:45:19+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 概率最大的路径


给你一个由 n 个节点（下标从 0 开始）组成的无向加权图，该图由一个描述边的列表组成，其中 edges[i] = [a, b] 表示连接节点 a 和 b 的一条无向边，且该边遍历成功的概率为 succProb[i] 。

指定两个节点分别作为起点 start 和终点 end ，请你找出从起点到终点成功概率最大的路径，并返回其成功概率。

如果不存在从 start 到 end 的路径，请 返回 0 。只要答案与标准答案的误差不超过 1e-5 ，就会被视作正确答案。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

/// 问题：给定无向图，每条边有一个成功概率（0~1），求从 start_node 到 end_node 的最大成功概率路径。
/// 路径概率为各边概率的乘积。使用 Dijkstra 变种（最大堆），将加法改为乘法，取最大概率。
impl Solution {
    pub fn max_probability(
        n: i32,
        edges: Vec<Vec<i32>>,
        succ_prob: Vec<f64>,
        start_node: i32,
        end_node: i32,
    ) -> f64 {
        let n = n as usize;
        let start = start_node as usize;
        let end = end_node as usize;

        // ---------- 1. 构建邻接表 ----------
        // graph[u] = [(v, probability), ...]
        let mut graph = vec![vec![]; n];
        for (edge, &prob) in edges.iter().zip(succ_prob.iter()) {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push((v, prob));
            graph[v].push((u, prob));
        }

        // ---------- 2. 初始化数据结构 ----------
        // best_prob[i] = 从起点到节点 i 的最大成功概率（初始为 0）
        let mut best_prob = vec![0.0; n];
        best_prob[start] = 1.0;

        // 使用最大堆（通过 Reverse 将最小堆转为最大堆，存储 (-概率, 节点) 或自定义 Ord）
        // 由于 f64 未实现 Ord，我们使用 Reverse 配合负号，但注意 -probability 可能为 -0.0，仍可比较。
        // 或者使用自定义结构体，但这里采用简洁的 (Reverse<f64>, node) 方案：
        //   Rust 的 BinaryHeap 是最大堆，但 Reverse 会将其变为最小堆，故我们要让“最大概率”最先弹出，
        //   可以存储 (Reverse(-prob), node) 等价于存储 (-prob, node) 利用 f64 的 PartialOrd。
        //   但 f64 没有 Ord，所以不能直接放入元组。因此我们保留原有的 MyF64 包装，但添加注释说明。
        //   为了代码自包含，我们内联一个简单的包装类型。
        #[derive(Clone, Copy, PartialEq, PartialOrd)]
        struct Prob(f64);
        impl Eq for Prob {}
        impl Ord for Prob {
            fn cmp(&self, other: &Self) -> std::cmp::Ordering {
                // 由于概率非负且不为 NaN，partial_cmp 总能得到 Some，直接 unwrap 安全
                self.partial_cmp(other).unwrap()
            }
        }

        let mut heap = BinaryHeap::new();
        heap.push((Prob(1.0), start));

        // ---------- 3. Dijkstra 变种（最大概率路径）----------
        while let Some((Prob(cur_prob), node)) = heap.pop() {
            // 到达终点直接返回（因为堆按概率降序，首次弹出的终点即为全局最优）
            if node == end {
                return cur_prob;
            }
            // 如果当前概率小于已知最佳概率，跳过（过时条目）
            if cur_prob < best_prob[node] {
                continue;
            }
            // 遍历所有邻居
            for &(next, edge_prob) in &graph[node] {
                let new_prob = cur_prob * edge_prob;
                if new_prob > best_prob[next] {
                    best_prob[next] = new_prob;
                    heap.push((Prob(new_prob), next));
                }
            }
        }

        // ---------- 4. 返回结果（若不可达则概率为 0）----------
        best_prob[end]
    }
}
```
