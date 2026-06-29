---
title: "leetcode-并查集51"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 统计完全连通分量的数量


给你一个整数 n 。现有一个包含 n 个顶点的 无向 图，顶点按从 0 到 n - 1 编号。给你一个二维整数数组 edges 其中 edges[i] = [ai, bi] 表示顶点 ai 和 bi 之间存在一条 无向 边。

返回图中 完全连通分量 的数量。

如果在子图中任意两个顶点之间都存在路径，并且子图中没有任何一个顶点与子图外部的顶点共享边，则称其为 连通分量 。

如果连通分量中每对节点之间都存在一条边，则称其为 完全连通分量 。

```
impl Solution {
    pub fn count_complete_components(n: i32, edges: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;
        let mut parent = vec![0; n];
        let mut size = vec![1; n];      // 连通分量中的节点数
        let mut edge_count = vec![0; n]; // 连通分量中的边数

        // 初始化并查集
        for i in 0..n {
            parent[i] = i;
        }

        // 查找根节点（路径压缩）
        fn find(parent: &mut Vec<usize>, x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        // 合并两个连通分量
        fn union(parent: &mut Vec<usize>, size: &mut Vec<usize>, edge_count: &mut Vec<usize>, a: usize, b: usize) {
            let ra = find(parent, a);
            let rb = find(parent, b);

            if ra == rb {
                // 在同一分量中，增加边数
                edge_count[ra] += 1;
                return;
            }

            // 按大小合并（将小树合并到大树）
            let (new_root, child) = if size[ra] >= size[rb] {
                (ra, rb)
            } else {
                (rb, ra)
            };

            parent[child] = new_root;
            size[new_root] += size[child];
            // 边数：两个分量的边数之和 + 当前边
            edge_count[new_root] += edge_count[child] + 1;
        }

        // 处理所有边
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            union(&mut parent, &mut size, &mut edge_count, u, v);
        }

        // 统计完全连通分量
        let mut result = 0;
        let mut visited = vec![false; n];

        for i in 0..n {
            let root = find(&mut parent, i);
            if visited[root] {
                continue;
            }
            visited[root] = true;

            let nodes = size[root];
            let edges = edge_count[root];
            // 完全连通图的条件：边数 = 节点数 * (节点数 - 1) / 2
            if edges == nodes * (nodes - 1) / 2 {
                result += 1;
            }
        }

        result
    }
}
```
