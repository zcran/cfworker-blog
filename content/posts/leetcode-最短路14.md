---
title: "leetcode-最短路14"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 设计可以求最短路径的图类


给你一个有 n 个节点的 有向带权 图，节点编号为 0 到 n - 1 。图中的初始边用数组 edges 表示，其中 edges[i] = [fromi, toi, edgeCosti] 表示从 fromi 到 toi 有一条代价为 edgeCosti 的边。

请你实现一个 Graph 类：

Graph(int n, int[][] edges) 初始化图有 n 个节点，并输入初始边。
addEdge(int[] edge) 向边集中添加一条边，其中 edge = [from, to, edgeCost] 。数据保证添加这条边之前对应的两个节点之间没有有向边。
int shortestPath(int node1, int node2) 返回从节点 node1 到 node2 的路径 最小 代价。如果路径不存在，返回 -1 。一条路径的代价是路径中所有边代价之和。


```
struct Graph {
    g: Vec<Vec<i32>>, // 邻接矩阵
}

impl Graph {
    fn new(n: i32, edges: Vec<Vec<i32>>) -> Self {
        let n = n as usize;
        let mut g = vec![vec![i32::MAX / 2; n]; n];
        for e in &edges {
            g[e[0] as usize][e[1] as usize] = e[2]; // 添加一条边（题目保证没有重边）
        }
        Self { g }
    }

    fn add_edge(&mut self, e: Vec<i32>) {
        self.g[e[0] as usize][e[1] as usize] = e[2]; // 添加一条边（题目保证这条边之前不存在）
    }

    fn shortest_path(&self, node1: i32, node2: i32) -> i32 {
        let n = self.g.len();
        let start = node1 as usize;
        let end = node2 as usize;
        // dis[i] 表示从起点 start 出发，到节点 i 的最短路长度
        let mut dis = vec![i32::MAX / 2; self.g.len()];
        dis[start] = 0;
        let mut vis = vec![false; n];
        loop {
            let mut x = n;
            for i in 0..n {
                if !vis[i] && (x == n || dis[i] < dis[x]) {
                    x = i;
                }
            }
            if x == n || dis[x] == i32::MAX / 2 { // 所有从 start 能到达的点都被更新了
                return -1; // 无法到达终点
            }
            if x == end { // 找到终点，提前退出
                return dis[x];
            }
            vis[x] = true; // 标记，在后续的循环中无需反复更新 x 到其余点的最短路长度
            for y in 0..n {
                dis[y] = dis[y].min(dis[x] + self.g[x][y]); // 更新最短路长度
            }
        }
    }
}

```
