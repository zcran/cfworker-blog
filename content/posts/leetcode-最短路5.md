---
title: "leetcode-最短路5"
date: 2026-04-22T20:45:19+08:00
tags: ["leetcode", "最短路"]
draft: false
---

## 阈值距离内邻居最少的城市


有 n 个城市，按从 0 到 n-1 编号。给你一个边数组 edges，其中 edges[i] = [fromi, toi, weighti] 代表 fromi 和 toi 两个城市之间的双向加权边，距离阈值是一个整数 distanceThreshold。

返回在路径距离限制为 distanceThreshold 以内可到达城市最少的城市。如果有多个这样的城市，则返回编号最大的城市。

注意，连接城市 i 和 j 的路径的距离等于沿该路径的所有边的权重之和。


```
use std::cmp::Reverse;          // 用于将 BinaryHeap 转换为最小堆
use std::collections::BinaryHeap;
use std::cmp::Ordering;         // 用于自定义比较逻辑

/// 辅助结构体，用于计算从某个起点出发，在距离阈值内可到达的节点数量
struct DistanceCounter<'a> {
    graph: &'a Vec<Vec<(usize, i32)>>,   // 邻接表：graph[u] = [(v, weight), ...]
    threshold: i32,                      // 距离阈值
}

impl<'a> DistanceCounter<'a> {
    /// 对指定起点执行 Dijkstra 算法，返回在阈值内可到达的其他节点数量（不包括起点自身）
    fn count_reachable_within_threshold(&mut self, start: usize) -> i32 {
        let n = self.graph.len();
        // 最短距离数组，初始化为最大值
        let mut dist = vec![i32::MAX; n];
        dist[start] = 0;

        // 最小堆存储 (距离, 节点)，使用 Reverse 包装使其成为最小堆
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, start)));

        // Dijkstra 主循环
        while let Some(Reverse((d, u))) = heap.pop() {
            // 如果当前弹出的距离大于记录的最短距离，说明是过期数据，跳过
            if d > dist[u] {
                continue;
            }
            // 遍历所有邻居节点
            for &(v, w) in &self.graph[u] {
                let nd = d + w;
                // 只有当新距离 <= 阈值，并且比已知距离更短时才更新
                if nd <= self.threshold && nd < dist[v] {
                    dist[v] = nd;
                    heap.push(Reverse((nd, v)));
                }
            }
        }

        // 统计所有距离 <= 阈值的节点数量，减去起点自身（因为起点距离为0，通常不算作“其他城市”）
        dist.iter()
            .filter(|&&d| d <= self.threshold)
            .count() as i32 - 1
    }
}

impl Solution {
    /// 找到城市数量最少的城市（在给定距离阈值内可到达的其他城市数最少）
    /// 如果有多个城市数量相同，返回编号最大的那个。
    ///
    /// # 参数
    /// - `n` : 城市数量（编号 0..n-1）
    /// - `edges` : 无向边列表，每条边为 [from, to, weight]
    /// - `distance_threshold` : 距离阈值
    pub fn find_the_city(n: i32, edges: Vec<Vec<i32>>, distance_threshold: i32) -> i32 {
        let n = n as usize;

        // ---------- 1. 构建邻接表 ----------
        let mut graph = vec![vec![]; n];
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let w = edge[2];
            graph[u].push((v, w));
            graph[v].push((u, w)); // 无向图，双向添加
        }

        // ---------- 2. 对每个城市分别计算阈值内可到达的城市数量 ----------
        let mut counter = DistanceCounter {
            graph: &graph,
            threshold: distance_threshold,
        };

        // 生成 (可到达数量, 城市编号) 的列表
        let results: Vec<(i32, usize)> = (0..n)
            .map(|city| (counter.count_reachable_within_threshold(city), city))
            .collect();

        // ---------- 3. 找到答案：数量最小的城市，数量相同时取编号较大者 ----------
        results
            .into_iter()
            .min_by(|&(cnt1, idx1), &(cnt2, idx2)| {
                // 先比较数量
                match cnt1.cmp(&cnt2) {
                    Ordering::Less => Ordering::Less,   // 数量更小，更优
                    Ordering::Greater => Ordering::Greater,
                    Ordering::Equal => {
                        // 数量相等时，编号较大的更优（题目要求返回最大的编号）
                        idx2.cmp(&idx1)  // idx1 < idx2 ? 则 idx2.cmp(&idx1) 为 Greater，符合需求
                    }
                }
            })
            .map(|(_, city)| city as i32)  // 提取城市编号并转换为 i32
            .unwrap()                      // 输入保证 n >= 1，所以 unwrap 安全
    }
}
```
