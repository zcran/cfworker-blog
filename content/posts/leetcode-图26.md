---
title: "leetcode-图26"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 到达首都的最少油耗

给你一棵 n 个节点的树（一个无向、连通、无环图），每个节点表示一个城市，编号从 0 到 n - 1 ，且恰好有 n - 1 条路。0 是首都。给你一个二维整数数组 roads ，其中 roads[i] = [ai, bi] ，表示城市 ai 和 bi 之间有一条 双向路 。

每个城市里有一个代表，他们都要去首都参加一个会议。

每座城市里有一辆车。给你一个整数 seats 表示每辆车里面座位的数目。

城市里的代表可以选择乘坐所在城市的车，或者乘坐其他城市的车。相邻城市之间一辆车的油耗是一升汽油。

请你返回到达首都最少需要多少升汽油。


```
impl Solution {
    pub fn minimum_fuel_cost(roads: Vec<Vec<i32>>, seats: i32) -> i64 {
        let n = roads.len() + 1;
        let seats = seats as i64;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for road in roads {
            let (u, v) = (road[0] as usize, road[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        let mut fuel = 0i64;
        Self::dfs(0, 0, &graph, seats, &mut fuel);
        fuel
    }

    /// 返回以 node 为根的子树大小
    /// 同时累计消耗的汽油：每个子树的代表需要向上移动一层，
    /// 所需的车辆数为 ceil(子树大小 / seats)
    fn dfs(node: usize, parent: usize, graph: &[Vec<usize>], seats: i64, fuel: &mut i64) -> i64 {
        let mut size = 1; // 包括当前节点

        for &next in &graph[node] {
            if next != parent {
                size += Self::dfs(next, node, graph, seats, fuel);
            }
        }

        // 除根节点外，每个子树都需要向上运输一次
        if node != 0 {
            // 需要的车辆数 = ceil(size / seats) = (size + seats - 1) / seats
            *fuel += (size + seats - 1) / seats;
        }

        size
    }
}
```
