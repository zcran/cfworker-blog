---
title: "leetcode-位掩码4"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 访问所有节点的最短路径

存在一个由 n 个节点组成的无向连通图，图中的节点按从 0 到 n - 1 编号。

给你一个数组 graph 表示这个图。其中，graph[i] 是一个列表，由所有与节点 i 直接相连的节点组成。

返回能够访问所有节点的最短路径的长度。你可以在任一节点开始和停止，也可以多次重访节点，并且可以重用边。

```
use std::collections::VecDeque;

impl Solution {
    pub fn shortest_path_length(graph: Vec<Vec<i32>>) -> i32 {
        let n = graph.len();
        let full_mask = (1 << n) - 1;
        let mut visited = vec![vec![false; 1 << n]; n];
        let mut queue = VecDeque::new();

        // 初始状态：每个节点为起点
        for start in 0..n {
            let mask = 1 << start;
            queue.push_back((start, mask, 0));
            visited[start][mask] = true;
        }

        while let Some((node, mask, dist)) = queue.pop_front() {
            if mask == full_mask {
                return dist;
            }

            // 函数式风格：用迭代器生成所有可能的新状态
            let new_states: Vec<(usize, usize, i32)> = graph[node]
                .iter()
                .map(|&next| next as usize)
                .map(|next| (next, mask | (1 << next), dist + 1))
                .collect();

            // 副作用：更新 visited 和队列（无法避免，但可集中处理）
            for (next, new_mask, new_dist) in new_states {
                if !visited[next][new_mask] {
                    visited[next][new_mask] = true;
                    queue.push_back((next, new_mask, new_dist));
                }
            }
        }

        unreachable!()
    }
}
```
