---
title: "leetcode-并查集43"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 统计无向图中无法互相到达点对数

给你一个整数 n ，表示一张 无向图 中有 n 个节点，编号为 0 到 n - 1 。同时给你一个二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示节点 ai 和 bi 之间有一条 无向 边。

请你返回 无法互相到达 的不同 点对数目 。


```
impl Solution {
    pub fn count_pairs(n: i32, edges: Vec<Vec<i32>>) -> i64 {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // DFS 计算连通分量大小
        let mut visited = vec![false; n];
        let mut total_visited = 0i64;
        let mut ans = 0i64;

        for i in 0..n {
            if !visited[i] {
                // 计算当前连通分量的大小
                let mut stack = vec![i];
                visited[i] = true;
                let mut size = 0;

                while let Some(u) = stack.pop() {
                    size += 1;
                    for &v in &graph[u] {
                        if !visited[v] {
                            visited[v] = true;
                            stack.push(v);
                        }
                    }
                }

                // 当前连通分量与其他已访问节点的配对数量
                ans += size as i64 * total_visited;
                total_visited += size as i64;
            }
        }

        ans
    }
}
```
