---
title: "leetcode-图47"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 最多 K 个连续相同字符的最短路径

给你一个整数 n，表示一个 有向加权 图中的节点数量，节点编号从 0 到 n - 1。该图由二维数组 edges 表示，其中 edges[i] = [ui, vi, wi] 表示一条从节点 ui 指向节点 vi、权重为 wi 的有向边。

另给定一个长度为 n 的字符串 labels，其中 labels[i] 是分配给节点 i 的字符，以及一个整数 k。

返回一条从节点 0 到节点 n - 1 的路径的 最小总边权 ，并要求该路径上所有节点标签按顺序 拼接 后，最多包含 k 个 连续相同 字符。如果不存在有效路径，返回 -1。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 寻找从 0 到 n-1 的最短路径，满足路径上标签拼接后连续相同字符数 ≤ k
    ///
    /// # 状态定义
    /// - `dis[x][c]`: 到达节点 `x` 且当前连续相同字符数为 `c` 时的最小总边权
    /// - 起点状态为 `(0, 0, 1)`（距离 0，节点 0，连续数 1）
    ///
    /// # 优化
    /// - 使用邻接表存储图
    /// - 使用 `BinaryHeap` 实现 Dijkstra，`Reverse` 构造最小堆
    /// - 提前返回：第一次弹出终点状态即为最短路径
    pub fn shortest_path(n: i32, edges: Vec<Vec<i32>>, labels: String, k: i32) -> i32 {
        let n = n as usize;
        let k = k as usize;
        let labels = labels.as_bytes(); // 字节级别比较，无需 UTF-8 解码

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let w = edge[2];
            graph[u].push((v, w));
        }

        const INF: i32 = i32::MAX;
        let mut dis = vec![vec![INF; k + 1]; n];
        dis[0][1] = 0; // 起点自身即一个连续字符

        // 最小堆：(距离, 节点, 连续相同字符数)
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, 0, 1)));

        while let Some(Reverse((d, x, cnt))) = heap.pop() {
            // 到达终点即返回，因为 Dijkstra 按距离递增弹出
            if x == n - 1 {
                return d;
            }
            // 跳过过时状态（已被更短距离更新过）
            if d > dis[x][cnt] {
                continue;
            }

            for &(y, w) in &graph[x] {
                // 计算新的连续相同字符数
                let new_cnt = if labels[y] == labels[x] { cnt + 1 } else { 1 };
                if new_cnt > k {
                    continue; // 剪枝：超过限制的路径无效
                }
                let new_d = d + w;
                if new_d < dis[y][new_cnt] {
                    dis[y][new_cnt] = new_d;
                    heap.push(Reverse((new_d, y, new_cnt)));
                }
            }
        }

        -1 // 没有有效路径
    }
}
```
