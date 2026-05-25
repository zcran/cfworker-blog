---
title: "leetcode-拓扑排序11"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 有向图中最大颜色值

给你一个 有向图 ，它含有 n 个节点和 m 条边。节点编号从 0 到 n - 1 。

给你一个字符串 colors ，其中 colors[i] 是小写英文字母，表示图中第 i 个节点的 颜色 （下标从 0 开始）。同时给你一个二维数组 edges ，其中 edges[j] = [aj, bj] 表示从节点 aj 到节点 bj 有一条 有向边 。

图中一条有效 路径 是一个点序列 x1 -> x2 -> x3 -> ... -> xk ，对于所有 1 <= i < k ，从 xi 到 xi+1 在图中有一条有向边。路径的 颜色值 是路径中 出现次数最多 颜色的节点数目。

请你返回给定图中有效路径里面的 最大颜色值 。如果图中含有环，请返回 -1 。

```
use std::collections::VecDeque;

impl Solution {
    /// 返回有向图中所有路径上出现次数最多的颜色的最大次数。
    /// 如果存在环（即不可能完成拓扑排序），返回 -1。
    pub fn largest_path_value(colors: String, edges: Vec<Vec<i32>>) -> i32 {
        let n = colors.len();
        let colors = colors.as_bytes();                 // 转为字节数组方便索引
        let mut graph = vec![Vec::new(); n];            // 邻接表
        let mut indegree = vec![0; n];                  // 入度

        // 建图，同时检测自环
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            if u == v { return -1; }                    // 自环直接构成环
            graph[u].push(v);
            indegree[v] += 1;
        }

        // 将所有入度为 0 的节点作为起始点入队
        let mut queue: VecDeque<usize> = (0..n)
            .filter(|&i| indegree[i] == 0)
            .collect();

        // cnt[node][color] = 到达当前节点时颜色 color 出现的最大次数
        let mut cnt = vec![vec![0; 26]; n];
        let mut max_color_cnt = 0;
        let mut visited_nodes = 0;

        // 拓扑排序
        while let Some(u) = queue.pop_front() {
            visited_nodes += 1;
            let color_idx = (colors[u] - b'a') as usize;
            cnt[u][color_idx] += 1;                     // 将当前节点的颜色计入
            max_color_cnt = max_color_cnt.max(cnt[u][color_idx]);

            // 更新所有后继节点
            for &v in &graph[u] {
                // 对于每种颜色，后继节点继承当前节点的最大计数
                for c in 0..26 {
                    cnt[v][c] = cnt[v][c].max(cnt[u][c]);
                }
                indegree[v] -= 1;
                if indegree[v] == 0 {
                    queue.push_back(v);
                }
            }
        }

        if visited_nodes < n { -1 } else { max_color_cnt }
    }
}
```
