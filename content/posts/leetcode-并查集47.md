---
title: "leetcode-并查集47"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 将节点分成尽可能多的组

给你一个正整数 n ，表示一个 无向 图中的节点数目，节点编号从 1 到 n 。

同时给你一个二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示节点 ai 和 bi 之间有一条 双向 边。注意给定的图可能是不连通的。

请你将图划分为 m 个组（编号从 1 开始），满足以下要求：

· 图中每个节点都只属于一个组。
· 图中每条边连接的两个点 [ai, bi] ，如果 ai 属于编号为 x 的组，bi 属于编号为 y 的组，那么 |y - x| = 1 。

请你返回最多可以将节点分为多少个组（也就是最大的 m ）。如果没办法在给定条件下分组，请你返回 -1 。


```
impl Solution {
    pub fn magnificent_sets(n: i32, edges: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let u = edge[0] as usize - 1;
            let v = edge[1] as usize - 1;
            graph[u].push(v);
            graph[v].push(u);
        }

        // 二分图判定 + 收集连通块节点
        let mut color = vec![0; n]; // 0:未染色, 1/-1:两种颜色
        let mut nodes = Vec::new();

        fn is_bipartite(
            x: usize,
            c: i8,
            graph: &[Vec<usize>],
            color: &mut [i8],
            nodes: &mut Vec<usize>
        ) -> bool {
            color[x] = c;
            nodes.push(x);
            for &y in &graph[x] {
                if color[y] == c {
                    return false;
                }
                if color[y] == 0 && !is_bipartite(y, -c, graph, color, nodes) {
                    return false;
                }
            }
            true
        }

        // BFS 求从 start 出发的最大深度
        let mut visited_time = vec![0; n];
        let mut clock = 0;

        let bfs_max_depth = |start: usize, graph: &[Vec<usize>], visited_time: &mut [i32], clock: &mut i32| -> i32 {
            *clock += 1;
            let mut depth = 0;
            let mut queue = vec![start];
            visited_time[start] = *clock;

            while !queue.is_empty() {
                let mut next = Vec::new();
                for &u in &queue {
                    for &v in &graph[u] {
                        if visited_time[v] != *clock {
                            visited_time[v] = *clock;
                            next.push(v);
                        }
                    }
                }
                queue = next;
                depth += 1;
            }
            depth
        };

        let mut ans = 0;
        let mut clock = 0;

        for i in 0..n {
            if color[i] != 0 {
                continue;
            }

            // 检查当前连通块是否为二分图
            nodes.clear();
            if !is_bipartite(i, 1, &graph, &mut color, &mut nodes) {
                return -1;
            }

            // 计算连通块的最大深度
            let mut max_depth = 0;
            for &node in &nodes {
                let depth = bfs_max_depth(node, &graph, &mut visited_time, &mut clock);
                max_depth = max_depth.max(depth);
            }
            ans += max_depth;
        }

        ans
    }
}
```
