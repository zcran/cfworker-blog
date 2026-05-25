---
title: "leetcode-最短路20"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 访问消失节点的最少时间

给你一个二维数组 edges 表示一个 n 个点的无向图，其中 edges[i] = [ui, vi, lengthi] 表示节点 ui 和节点 vi 之间有一条需要 lengthi 单位时间通过的无向边。

同时给你一个数组 disappear ，其中 disappear[i] 表示节点 i 从图中消失的时间点，在那一刻及以后，你无法再访问这个节点。

注意，图有可能一开始是不连通的，两个节点之间也可能有多条边。

请你返回数组 answer ，answer[i] 表示从节点 0 到节点 i 需要的 最少 单位时间。如果从节点 0 出发 无法 到达节点 i ，那么 answer[i] 为 -1 。



```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn minimum_time(n: i32, edges: Vec<Vec<i32>>, disappear: Vec<i32>) -> Vec<i32> {
        let mut adj = vec![Vec::new(); n as usize];
        for edge in edges.iter() {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let length = edge[2];
            adj[u].push((v, length));
            adj[v].push((u, length));
        }

        let mut pq = BinaryHeap::new();
        pq.push(Reverse((0, 0)));
        let mut answer = vec![-1; n as usize];
        answer[0] = 0;

        while let Some(Reverse((t, u))) = pq.pop() {
            if t != answer[u] {
                continue;
            }
            for &(v, length) in adj[u].iter() {
                if t + length < disappear[v] && (answer[v] == -1 || t + length < answer[v]) {
                    pq.push(Reverse((t + length, v)));
                    answer[v] = t + length;
                }
            }
        }

        answer
    }
}

```
