---
title: "leetcode-回溯31"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 所有可能的路径

给你一个有 n 个节点的 有向无环图（DAG），请你找出从节点 0 到节点 n-1 的所有路径并输出（不要求按特定顺序）

 graph[i] 是一个从节点 i 可以访问的所有节点的列表（即从节点 i 到节点 graph[i][j]存在一条有向边）。


```
impl Solution {
    /// 返回从节点 0 到节点 n-1 的所有路径
    ///
    /// 思路：DFS 回溯，从起点 0 出发，遍历所有可达节点，到达终点时记录路径
    pub fn all_paths_source_target(graph: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let n = graph.len();
        let target = n - 1;
        let mut result = Vec::new();
        let mut path = vec![0]; // 路径从节点 0 开始

        fn dfs(
            graph: &[Vec<i32>],
            target: usize,
            node: usize,
            path: &mut Vec<i32>,
            result: &mut Vec<Vec<i32>>,
        ) {
            // 到达目标节点，记录当前路径
            if node == target {
                result.push(path.clone());
                return;
            }

            // 遍历当前节点的所有邻居
            for &next in &graph[node] {
                let next = next as usize;
                path.push(next as i32);
                dfs(graph, target, next, path, result);
                path.pop(); // 回溯
            }
        }

        dfs(&graph, target, 0, &mut path, &mut result);
        result
    }
}
```
