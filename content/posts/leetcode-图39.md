---
title: "leetcode-图39"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 构造符合图结构的二维矩阵

给你一个二维整数数组 edges ，它表示一棵 n 个节点的 无向 图，其中 edges[i] = [ui, vi] 表示节点 ui 和 vi 之间有一条边。

请你构造一个二维矩阵，满足以下条件：

· 矩阵中每个格子 一一对应 图中 0 到 n - 1 的所有节点。
· 矩阵中两个格子相邻（横 的或者 竖 的）当且仅当 它们对应的节点在 edges 中有边连接。

题目保证 edges 可以构造一个满足上述条件的二维矩阵。

请你返回一个符合上述要求的二维整数数组，如果存在多种答案，返回任意一个。


```
impl Solution {
    pub fn construct_grid_layout(n: i32, edges: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // 记录每个度数的代表性节点（度数范围为 1~4）
        let mut deg_node = [usize::MAX; 5];
        for (i, neighbors) in graph.iter().enumerate() {
            deg_node[neighbors.len()] = i;
        }

        // 构造第一行（存储为 i32）
        let mut first_row: Vec<i32> = Vec::new();
        if deg_node[1] != usize::MAX {
            // 只有一列的情况
            first_row.push(deg_node[1] as i32);
        } else if deg_node[4] == usize::MAX {
            // 只有两列的情况：找两个度数为 2 且相邻的节点
            let start = deg_node[2];
            for &neighbor in &graph[start] {
                if graph[neighbor].len() == 2 {
                    first_row = vec![start as i32, neighbor as i32];
                    break;
                }
            }
        } else {
            // 至少三列：找度数为 2 → 3 → 3 → ... → 2 的路径作为第一行
            let mut start = deg_node[2];
            first_row.push(start as i32);
            let mut prev = start;
            start = graph[start][0];

            while graph[start].len() == 3 {
                first_row.push(start as i32);
                for &next in &graph[start] {
                    if next != prev && graph[next].len() < 4 {
                        prev = start;
                        start = next;
                        break;
                    }
                }
            }
            first_row.push(start as i32); // 最后一个节点度数为 2
        }

        // 标记已访问
        let mut visited = vec![false; n];
        for &node in &first_row {
            visited[node as usize] = true;
        }

        // 逐行构造矩阵
        let rows = n / first_row.len();
        let mut ans: Vec<Vec<i32>> = Vec::with_capacity(rows);
        ans.push(first_row);

        for i in 1..rows {
            let mut current_row = Vec::with_capacity(ans[0].len());
            for &above in &ans[i - 1] {
                let above = above as usize;
                // 从当前节点的邻居中，找唯一未访问的节点（即下方的节点）
                for &neighbor in &graph[above] {
                    if !visited[neighbor] {
                        visited[neighbor] = true;
                        current_row.push(neighbor as i32);
                        break;
                    }
                }
            }
            ans.push(current_row);
        }

        ans
    }
}
```
