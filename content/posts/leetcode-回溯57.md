---
title: "leetcode-回溯57"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 最大化一张图中的路径价值

给你一张 无向 图，图中有 n 个节点，节点编号从 0 到 n - 1 （都包括）。同时给你一个下标从 0 开始的整数数组 values ，其中 values[i] 是第 i 个节点的 价值 。同时给你一个下标从 0 开始的二维整数数组 edges ，其中 edges[j] = [uj, vj, timej] 表示节点 uj 和 vj 之间有一条需要 timej 秒才能通过的无向边。最后，给你一个整数 maxTime 。

合法路径 指的是图中任意一条从节点 0 开始，最终回到节点 0 ，且花费的总时间 不超过 maxTime 秒的一条路径。你可以访问一个节点任意次。一条合法路径的 价值 定义为路径中 不同节点 的价值 之和 （每个节点的价值 至多 算入价值总和中一次）。

请你返回一条合法路径的 最大 价值。

注意：每个节点 至多 有 四条 边与之相连。


```
impl Solution {
    pub fn maximal_path_quality(values: Vec<i32>, edges: Vec<Vec<i32>>, max_time: i32) -> i32 {
        let n = values.len();

        // 构建邻接表：每个节点存储 (邻居, 耗时)
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (u, v, t) = (edge[0] as usize, edge[1] as usize, edge[2]);
            graph[u].push((v, t));
            graph[v].push((u, t));
        }

        let mut visited = vec![false; n];
        visited[0] = true;
        let mut max_value = 0;

        // DFS 搜索所有合法路径
        fn dfs(
            node: usize,           // 当前节点
            time: i32,             // 已花费时间
            value: i32,            // 当前路径价值
            graph: &Vec<Vec<(usize, i32)>>,
            visited: &mut Vec<bool>,
            values: &Vec<i32>,
            max_time: i32,
            max_value: &mut i32,
        ) {
            // 回到起点时更新答案
            if node == 0 {
                *max_value = (*max_value).max(value);
            }

            // 剪枝：如果已经达到理论最大值（所有节点价值之和），可以提前返回
            // 但这里不强制，因为可能无法访问所有节点

            for &(next, cost) in &graph[node] {
                let new_time = time + cost;
                if new_time > max_time {
                    continue; // 超时，跳过
                }

                if !visited[next] {
                    // 首次访问新节点，累加价值
                    visited[next] = true;
                    dfs(
                        next,
                        new_time,
                        value + values[next],
                        graph,
                        visited,
                        values,
                        max_time,
                        max_value,
                    );
                    visited[next] = false;
                } else {
                    // 已访问节点，不重复计算价值
                    dfs(
                        next,
                        new_time,
                        value,
                        graph,
                        visited,
                        values,
                        max_time,
                        max_value,
                    );
                }
            }
        }

        dfs(0, 0, values[0], &graph, &mut visited, &values, max_time, &mut max_value);
        max_value
    }
}
```
