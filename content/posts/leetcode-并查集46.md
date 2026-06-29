---
title: "leetcode-并查集46"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 两个城市间路径的最小分数

给你一个正整数 n ，表示总共有 n 个城市，城市从 1 到 n 编号。给你一个二维数组 roads ，其中 roads[i] = [ai, bi, distancei] 表示城市 ai 和 bi 之间有一条 双向 道路，道路距离为 distancei 。城市构成的图不一定是连通的。

两个城市之间一条路径的 分数 定义为这条路径中道路的 最小 距离。

返回城市 1 和城市 n 之间的所有路径的 最小 分数。

注意：

一条路径指的是两个城市之间的道路序列。

一条路径可以 多次 包含同一条道路，你也可以沿着路径多次到达城市 1 和城市 n 。

测试数据保证城市 1 和城市n 之间 至少 有一条路径。


```
impl Solution {
    pub fn min_score(n: i32, roads: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n + 1];
        for road in roads {
            let u = road[0] as usize;
            let v = road[1] as usize;
            let d = road[2];
            graph[u].push((v, d));
            graph[v].push((u, d));
        }

        // DFS 遍历与城市 1 连通的所有节点，记录最小边权
        let mut visited = vec![false; n + 1];
        let mut stack = vec![1];
        visited[1] = true;
        let mut min_dist = i32::MAX;

        while let Some(u) = stack.pop() {
            for &(v, dist) in &graph[u] {
                min_dist = min_dist.min(dist);
                if !visited[v] {
                    visited[v] = true;
                    stack.push(v);
                }
            }
        }

        min_dist
    }
}
```
