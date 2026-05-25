---
title: "leetcode-最短路9"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 到达目的地的方案数


你在一个城市里，城市由 n 个路口组成，路口编号为 0 到 n - 1 ，某些路口之间有 双向 道路。输入保证你可以从任意路口出发到达其他任意路口，且任意两个路口之间最多有一条路。

给你一个整数 n 和二维整数数组 roads ，其中 roads[i] = [ui, vi, timei] 表示在路口 ui 和 vi 之间有一条需要花费 timei 时间才能通过的道路。你想知道花费 最少时间 从路口 0 出发到达路口 n - 1 的方案数。

请返回花费 最少时间 到达目的地的 路径数目 。由于答案可能很大，将结果对 109 + 7 取余 后返回。


```
impl Solution {
    /// 计算从节点 0 到节点 n-1 的最短路径数量（模 1_000_000_007）
    /// 使用朴素 Dijkstra 算法（邻接矩阵 + 线性扫描），边权均为正数。
    pub fn count_paths(n: i32, roads: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = n as usize;
        let target = n - 1;

        // ---------- 1. 初始化邻接矩阵 ----------
        // 使用 i64 避免加法溢出，INF 为 i64::MAX / 2
        let inf = i64::MAX / 2;
        let mut graph = vec![vec![inf; n]; n];
        for road in &roads {
            let u = road[0] as usize;
            let v = road[1] as usize;
            let w = road[2] as i64;
            graph[u][v] = w;
            graph[v][u] = w;
        }

        // ---------- 2. 初始化距离数组、路径计数数组和访问标记 ----------
        let mut dist = vec![inf; n];
        dist[0] = 0;
        let mut count = vec![0; n];
        count[0] = 1;
        let mut visited = vec![false; n];

        // 辅助函数：在未访问的节点中找到距离最小的节点
        fn find_nearest(dist: &[i64], visited: &[bool]) -> Option<usize> {
            let mut min_node = None;
            let mut min_dist = i64::MAX;
            for (i, &d) in dist.iter().enumerate() {
                if !visited[i] && d < min_dist {
                    min_dist = d;
                    min_node = Some(i);
                }
            }
            min_node
        }

        // ---------- 3. Dijkstra 主循环 ----------
        while let Some(u) = find_nearest(&dist, &visited) {
            // 如果当前取出的节点是目标节点，由于边权为正，后续节点距离只会更大，
            // 因此可以直接返回目标节点的路径计数。
            if u == target {
                return (count[target] % MOD) as i32;
            }
            visited[u] = true; // 标记 u 的最短距离已确定

            // 尝试通过 u 松弛所有邻居 v
            for v in 0..n {
                let weight = graph[u][v];
                if weight == inf {
                    continue; // 没有直接边
                }
                let new_dist = dist[u] + weight;
                if new_dist < dist[v] {
                    // 找到更短路径，更新距离并重置计数
                    dist[v] = new_dist;
                    count[v] = count[u];
                } else if new_dist == dist[v] {
                    // 找到另一条同等长度的最短路径，累加计数
                    count[v] = (count[v] + count[u]) % MOD;
                }
            }
        }

        // 若循环结束仍未到达目标（图不连通），返回 0（原题保证连通，此处为防御性编程）
        (count[target] % MOD) as i32
    }
}
```
