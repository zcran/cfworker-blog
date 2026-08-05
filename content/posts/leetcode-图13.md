---
title: "leetcode-图13"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 重新规划路线

n 座城市，从 0 到 n-1 编号，其间共有 n-1 条路线。因此，要想在两座不同城市之间旅行只有唯一一条路线可供选择（路线网形成一颗树）。去年，交通运输部决定重新规划路线，以改变交通拥堵的状况。

路线用 connections 表示，其中 connections[i] = [a, b] 表示从城市 a 到 b 的一条有向路线。

今年，城市 0 将会举办一场大型比赛，很多游客都想前往城市 0 。

请你帮助重新规划路线方向，使每个城市都可以访问城市 0 。返回需要变更方向的最小路线数。

题目数据 保证 每个城市在重新规划路线方向后都能到达城市 0 。


```
impl Solution {
    pub fn min_reorder(n: i32, connections: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 构建双向图：正向边标记为 1（需要反转），反向边标记为 0（不需要反转）
        let mut graph = vec![vec![]; n];
        for e in connections {
            let (u, v) = (e[0] as usize, e[1] as usize);
            graph[u].push((v, 1)); // 原始方向：u -> v，需要反转才能到达 0
            graph[v].push((u, 0)); // 反向：v -> u，不需要反转
        }

        // DFS 从根节点 0 开始遍历，统计需要反转的边数
        fn dfs(u: usize, parent: usize, graph: &[Vec<(usize, i32)>]) -> i32 {
            let mut ans = 0;
            for &(v, need_reverse) in &graph[u] {
                if v == parent { continue; }
                ans += need_reverse + dfs(v, u, graph);
            }
            ans
        }

        dfs(0, n, &graph)
    }
}
```
