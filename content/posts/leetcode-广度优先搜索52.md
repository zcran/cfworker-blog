---
title: "leetcode-广度优先搜索50"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 节点间通路

节点间通路。给定有向图，设计一个算法，找出两个节点之间是否存在一条路径。

示例 1：

 输入：n = 3, graph = [[0, 1], [0, 2], [1, 2], [1, 2]], start = 0, target = 2
 输出：true

示例 2：

 输入：n = 5, graph = [[0, 1], [0, 2], [0, 4], [0, 4], [0, 1], [1, 3], [1, 4], [1, 3], [2, 3], [3, 4]], start = 0, target = 4
 输出：true

提示：

节点数量n在[0, 1e5]范围内。
节点编号大于等于 0 小于 n。
图中可能存在自环和平行边。

```
use std::collections::VecDeque;

impl Solution {
    pub fn find_whether_exists_path(n: i32, graph: Vec<Vec<i32>>, start: i32, target: i32) -> bool {
        // 起点即终点，直接连通
        if start == target {
            return true;
        }

        // 构建邻接表
        let n = n as usize;
        let mut adj = vec![Vec::new(); n];
        for e in &graph {
            adj[e[0] as usize].push(e[1] as usize);
        }

        // BFS 从 start 出发，看能否到达 target
        let mut visited = vec![false; n];
        visited[start as usize] = true;

        let mut queue = VecDeque::new();
        queue.push_back(start as usize);

        while let Some(node) = queue.pop_front() {
            for &next in &adj[node] {
                if next == target as usize {
                    return true;
                }
                if !visited[next] {
                    visited[next] = true;
                    queue.push_back(next);
                }
            }
        }

        false
    }
}
```
