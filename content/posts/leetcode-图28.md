---
title: "leetcode-图28"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 添加边使所有节点度数都为偶数

给你一个有 n 个节点的 无向 图，节点编号为 1 到 n 。再给你整数 n 和一个二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示节点 ai 和 bi 之间有一条边。图不一定连通。

你可以给图中添加 至多 两条额外的边（也可以一条边都不添加），使得图中没有重边也没有自环。

如果添加额外的边后，可以使得图中所有点的度数都是偶数，返回 true ，否则返回 false 。

点的度数是连接一个点的边的数目。


```
impl Solution {
    pub fn is_possible(n: i32, edges: Vec<Vec<i32>>) -> bool {
        let n = n as usize;

        // 使用邻接矩阵的布尔向量来快速判断边是否存在
        // 每个节点用一个 HashSet 存储邻居
        let mut graph = vec![std::collections::HashSet::new(); n + 1];
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].insert(v);
            graph[v].insert(u);
        }

        // 收集度数为奇数的节点
        let odd_nodes: Vec<usize> = (1..=n)
            .filter(|&i| graph[i].len() % 2 == 1)
            .collect();

        match odd_nodes.len() {
            0 => true,  // 已经全部偶数度

            2 => {
                let (x, y) = (odd_nodes[0], odd_nodes[1]);
                // 如果 x 和 y 没有边，直接连接它们即可
                if !graph[x].contains(&y) {
                    return true;
                }
                // x 和 y 有边，找一个与两者都无边的节点作为中转
                for i in 1..=n {
                    if i != x && i != y && !graph[i].contains(&x) && !graph[i].contains(&y) {
                        return true;
                    }
                }
                false
            }

            4 => {
                let [a, b, c, d] = [odd_nodes[0], odd_nodes[1], odd_nodes[2], odd_nodes[3]];
                // 尝试三种配对方式，需要每对节点之间都没有边
                (!graph[a].contains(&b) && !graph[c].contains(&d)) ||
                (!graph[a].contains(&c) && !graph[b].contains(&d)) ||
                (!graph[a].contains(&d) && !graph[b].contains(&c))
            }

            _ => false,  // 奇数度节点数量不为 0, 2, 4 则不可能
        }
    }
}
```
