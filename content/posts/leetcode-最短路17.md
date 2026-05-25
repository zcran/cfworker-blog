---
title: "leetcode-最短路17"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 关闭分部的可行集合数目

一个公司在全国有 n 个分部，它们之间有的有道路连接。一开始，所有分部通过这些道路两两之间互相可以到达。

公司意识到在分部之间旅行花费了太多时间，所以它们决定关闭一些分部（也可能不关闭任何分部），同时保证剩下的分部之间两两互相可以到达且最远距离不超过 maxDistance 。

两个分部之间的 距离 是通过道路长度之和的 最小值 。

给你整数 n ，maxDistance 和下标从 0 开始的二维整数数组 roads ，其中 roads[i] = [ui, vi, wi] 表示一条从 ui 到 vi 长度为 wi的 无向 道路。

请你返回关闭分部的可行方案数目，满足每个方案里剩余分部之间的最远距离不超过 maxDistance。

注意，关闭一个分部后，与之相连的所有道路不可通行。

注意，两个分部之间可能会有多条道路。


```
impl Solution {
    /// 统计满足条件的子集数量：子集中任意两个节点的最短距离不超过 max_distance。
    /// 使用位掩码枚举所有子集，对每个子集运行 Floyd-Warshall 求最短路径后验证。
    pub fn number_of_sets(n: i32, max_distance: i32, roads: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;
        let max_dist = max_distance as usize;
        let mut valid_count = 0;

        // 预存储道路信息，避免重复类型转换
        let roads: Vec<(usize, usize, usize)> = roads
            .iter()
            .map(|r| (r[0] as usize, r[1] as usize, r[2] as usize))
            .collect();

        // 枚举所有子集 (2^n 种可能)
        for mask in 0..(1 << n) {
            // 判断每个节点是否属于当前子集
            let is_open: Vec<bool> = (0..n).map(|i| (mask >> i) & 1 == 1).collect();

            // 初始化距离矩阵: 不相邻的节点距离设为一个大数 (这里原代码用 1_000_000)
            let mut dist = vec![vec![1_000_000; n]; n];
            // 自己到自己的距离为 0
            for i in 0..n {
                dist[i][i] = 0;
            }

            // 根据当前子集，仅保留两端都在子集中的道路
            for &(u, v, w) in &roads {
                if is_open[u] && is_open[v] {
                    if w < dist[u][v] {
                        dist[u][v] = w;
                        dist[v][u] = w;
                    }
                }
            }

            // Floyd-Warshall 计算所有节点对的最短距离（仅考虑子集内节点）
            for k in 0..n {
                if !is_open[k] {
                    continue;
                }
                for i in 0..n {
                    if !is_open[i] {
                        continue;
                    }
                    // 由于对称性， j 从 i+1 开始，同时更新 dist[i][j] 和 dist[j][i]
                    for j in i + 1..n {
                        if !is_open[j] {
                            continue;
                        }
                        let via_k = dist[i][k] + dist[k][j];
                        if via_k < dist[i][j] {
                            dist[i][j] = via_k;
                            dist[j][i] = via_k;
                        }
                    }
                }
            }

            // 验证子集内所有节点对的最短距离是否 ≤ max_distance
            let is_valid = (0..n)
                .filter(|&i| is_open[i])
                .all(|i| {
                    (i + 1..n)
                        .filter(|&j| is_open[j])
                        .all(|j| dist[i][j] <= max_dist)
                });

            if is_valid {
                valid_count += 1;
            }
        }

        valid_count
    }
}
```
