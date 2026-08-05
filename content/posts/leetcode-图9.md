---
title: "leetcode-图9"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 查找集群内的关键连接

力扣数据中心有 n 台服务器，分别按从 0 到 n-1 的方式进行了编号。它们之间以 服务器到服务器 的形式相互连接组成了一个内部集群，连接是无向的。用  connections 表示集群网络，connections[i] = [a, b] 表示服务器 a 和 b 之间形成连接。任何服务器都可以直接或者间接地通过网络到达任何其他服务器。

关键连接 是在该集群中的重要连接，假如我们将它移除，便会导致某些服务器无法访问其他服务器。

请你以任意顺序返回该集群内的所有 关键连接 。


```
impl Solution {
    pub fn critical_connections(n: i32, connections: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![vec![]; n];
        for e in connections {
            let (u, v) = (e[0] as usize, e[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // Tarjan 算法找桥
        let mut dfn = vec![0; n];   // 发现时间
        let mut low = vec![0; n];   // 可达的最早祖先
        let mut time = 0;
        let mut bridges = vec![];

        fn dfs(
            u: usize,
            parent: usize,
            graph: &[Vec<usize>],
            dfn: &mut [i32],
            low: &mut [i32],
            time: &mut i32,
            bridges: &mut Vec<Vec<i32>>,
        ) {
            *time += 1;
            dfn[u] = *time;
            low[u] = *time;

            for &v in &graph[u] {
                if v == parent { continue; }

                if dfn[v] == 0 {
                    // 未访问，递归子节点
                    dfs(v, u, graph, dfn, low, time, bridges);
                    low[u] = low[u].min(low[v]);

                    // 桥判定：子节点无法回到 u 的祖先
                    if low[v] > dfn[u] {
                        bridges.push(vec![u as i32, v as i32]);
                    }
                } else {
                    // 已访问，更新 low
                    low[u] = low[u].min(dfn[v]);
                }
            }
        }

        // 对每个连通分量执行 Tarjan
        for i in 0..n {
            if dfn[i] == 0 {
                dfs(i, i, &graph, &mut dfn, &mut low, &mut time, &mut bridges);
            }
        }

        bridges
    }
}
```
