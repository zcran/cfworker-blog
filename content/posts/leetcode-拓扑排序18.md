---
title: "leetcode-拓扑排序18"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 图中的最长环

给你一个 n 个节点的 有向图 ，节点编号为 0 到 n - 1 ，其中每个节点 至多 有一条出边。

图用一个大小为 n 下标从 0 开始的数组 edges 表示，节点 i 到节点 edges[i] 之间有一条有向边。如果节点 i 没有出边，那么 edges[i] == -1 。

请你返回图中的 最长 环，如果没有任何环，请返回 -1 。

一个环指的是起点和终点是 同一个 节点的路径。

```
impl Solution {
    /// 返回有向图中最大环的长度，若不存在环则返回 -1。
    /// 图由 `edges` 数组表示：`edges[i]` 表示节点 i 的出边指向的节点，若无出边则值为 -1。
    pub fn longest_cycle(edges: Vec<i32>) -> i32 {
        let n = edges.len();
        // visit_time[node] 记录节点首次被访问的时间戳（从 1 开始计数的全局步数）
        let mut visit_time = vec![0; n];
        let mut time = 0;           // 全局时间戳计数器
        let mut max_cycle_len = -1;

        for start_node in 0..n {
            if visit_time[start_node] != 0 {
                continue;           // 已访问过的节点无需再作为起点
            }
            let start_time = time;  // 记录本次遍历的起始时间戳
            let mut cur = start_node as i32;
            // 沿着边走下去，直到遇到无出边或已访问节点
            while cur != -1 {
                let u = cur as usize;
                time += 1;
                if visit_time[u] != 0 {
                    // 遇到已访问节点，检查是否属于本次 DFS 过程中发现的环
                    if visit_time[u] > start_time {
                        let cycle_len = time - visit_time[u];
                        max_cycle_len = max_cycle_len.max(cycle_len as i32);
                    }
                    break;
                }
                visit_time[u] = time;
                cur = edges[u];
            }
        }
        max_cycle_len
    }
}
```
