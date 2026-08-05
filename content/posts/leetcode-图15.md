---
title: "leetcode-图15"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 最大网络秩

n 座城市和一些连接这些城市的道路 roads 共同组成一个基础设施网络。每个 roads[i] = [ai, bi] 都表示在城市 ai 和 bi 之间有一条双向道路。

两座不同城市构成的 城市对 的 网络秩 定义为：与这两座城市 直接 相连的道路总数。如果存在一条道路直接连接这两座城市，则这条道路只计算 一次 。

整个基础设施网络的 最大网络秩 是所有不同城市对中的 最大网络秩 。

给你整数 n 和数组 roads，返回整个基础设施网络的 最大网络秩 。


```
impl Solution {
    pub fn maximal_network_rank(n: i32, roads: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 邻接矩阵：connected[i][j] = 1 表示城市 i 和 j 直接相连
        let mut connected = vec![vec![0; n]; n];
        // 每个城市的度（直接相连的道路数量）
        let mut degree = vec![0; n];

        // 构建图：记录每条边并更新度数
        for road in roads {
            let (u, v) = (road[0] as usize, road[1] as usize);
            connected[u][v] = 1;
            connected[v][u] = 1;
            degree[u] += 1;
            degree[v] += 1;
        }

        let mut max_rank = 0;

        // 枚举所有城市对 (i, j)，计算它们的网络秩
        for i in 0..n {
            for j in i + 1..n {
                // 网络秩 = 两个城市的度之和 - (如果直接相连则减 1)
                let rank = degree[i] + degree[j] - connected[i][j];
                max_rank = max_rank.max(rank);
            }
        }

        max_rank
    }
}
```
