---
title: "leetcode-广度优先搜索46"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 树上的勾股距离节点

给你一个整数 n 和一棵包含 n 个节点的无向树，节点编号从 0 到 n - 1。该树由一个长度为 n - 1 的二维数组 edges 表示，其中 edges[i] = [ui, vi] 表示 ui 和 vi 之间存在一条无向边。

另给你三个 互不相同 的目标节点 x、y 和 z。

对于树中的任意节点 u：

令 dx 为 u 到节点 x 的距离
令 dy 为 u 到节点 y 的距离
令 dz 为 u 到节点 z 的距离

如果这三个距离形成一个 勾股数元组 ，则称节点 u 为 特殊 节点。

返回一个整数，表示树中特殊节点的数量。

勾股数元组 由三个整数 a、b 和 c 组成，当它们按 升序 排列时，满足 a2 + b2 = c2。

树中两个节点之间的 距离 是它们之间唯一路径上的边数。


```
impl Solution {
    pub fn special_nodes(n: i32, edges: Vec<Vec<i32>>, x: i32, y: i32, z: i32) -> i32 {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for e in &edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // 从 start 出发做一次 DFS，返回所有节点到 start 的距离
        fn distances(start: usize, graph: &[Vec<usize>]) -> Vec<i32> {
            let n = graph.len();
            let mut dist = vec![0i32; n];
            let mut stack = vec![(start, usize::MAX)];
            while let Some((node, parent)) = stack.pop() {
                for &next in &graph[node] {
                    if next == parent {
                        continue;
                    }
                    dist[next] = dist[node] + 1;
                    stack.push((next, node));
                }
            }
            dist
        }

        let dx = distances(x as usize, &graph);
        let dy = distances(y as usize, &graph);
        let dz = distances(z as usize, &graph);

        // 统计满足勾股关系的节点数
        (0..n)
            .filter(|&i| {
                let mut d = [dx[i], dy[i], dz[i]];
                d.sort_unstable();
                let (a, b, c) = (d[0] as i64, d[1] as i64, d[2] as i64);
                a * a + b * b == c * c
            })
            .count() as i32
    }
}
```
