---
title: "leetcode-并查集38"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 寻找图中是否存在路径

有一个具有 n 个顶点的 双向 图，其中每个顶点标记从 0 到 n - 1（包含 0 和 n - 1）。图中的边用一个二维整数数组 edges 表示，其中 edges[i] = [ui, vi] 表示顶点 ui 和顶点 vi 之间的双向边。 每个顶点对由 最多一条 边连接，并且没有顶点存在与自身相连的边。

请你确定是否存在从顶点 source 开始，到顶点 destination 结束的 有效路径 。

给你数组 edges 和整数 n、source 和 destination，如果从 source 到 destination 存在 有效路径 ，则返回 true，否则返回 false 。


```
impl Solution {
    pub fn valid_path(n: i32, edges: Vec<Vec<i32>>, source: i32, destination: i32) -> bool {
        // 并查集：用于判断两个顶点是否连通
        let mut parent = (0..n as usize).collect::<Vec<_>>();

        // 路径压缩查找
        fn find(parent: &mut [usize], x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        // 合并两个集合（按秩合并优化）
        let mut rank = vec![0; n as usize];
        for edge in edges {
            let a = find(&mut parent, edge[0] as usize);
            let b = find(&mut parent, edge[1] as usize);
            if a != b {
                if rank[a] < rank[b] {
                    parent[a] = b;
                } else if rank[a] > rank[b] {
                    parent[b] = a;
                } else {
                    parent[b] = a;
                    rank[a] += 1;
                }
            }
        }

        // 判断 source 和 destination 是否在同一个连通分量中
        find(&mut parent, source as usize) == find(&mut parent, destination as usize)
    }
}
```
