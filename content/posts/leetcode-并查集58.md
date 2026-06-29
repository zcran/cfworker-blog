---
title: "leetcode-并查集58"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 包含 K 个连通分量需要的最小时间

给你一个整数 n，表示一个包含 n 个节点（从 0 到 n - 1 编号）的无向图。该图由一个二维数组 edges 表示，其中 edges[i] = [ui, vi, timei] 表示一条连接节点 ui 和节点 vi 的无向边，该边会在时间 timei 被移除。

同时，另给你一个整数 k。

最初，图可能是连通的，也可能是非连通的。你的任务是找到一个 最小 的时间 t，使得在移除所有满足条件 time <= t 的边之后，该图包含 至少 k 个连通分量。

返回这个 最小 时间 t。

连通分量 是图的一个子图，其中任意两个顶点之间都存在路径，且子图中的任意顶点均不与子图外的顶点共享边。


```
struct UnionFind {
    parent: Vec<usize>,
    rank: Vec<usize>,
    count: usize,
}

impl UnionFind {
    fn new(n: usize) -> Self {
        Self {
            parent: (0..n).collect(),
            rank: vec![0; n],
            count: n,
        }
    }

    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    fn union(&mut self, a: usize, b: usize) {
        let ra = self.find(a);
        let rb = self.find(b);
        if ra == rb {
            return;
        }
        // 按秩合并
        if self.rank[ra] < self.rank[rb] {
            self.parent[ra] = rb;
        } else {
            self.parent[rb] = ra;
            if self.rank[ra] == self.rank[rb] {
                self.rank[ra] += 1;
            }
        }
        self.count -= 1;
    }
}

impl Solution {
    pub fn min_time(n: i32, mut edges: Vec<Vec<i32>>, k: i32) -> i32 {
        let n = n as usize;
        let k = k as usize;

        // 按时间降序排序（时间大的先处理）
        edges.sort_unstable_by(|a, b| b[2].cmp(&a[2]));

        let mut uf = UnionFind::new(n);
        let mut components = n;

        // 从最大的时间开始，尝试保留边
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;

            uf.union(u, v);
            components = uf.count;

            // 如果连通分量小于 k，说明当前边是导致连通性增加的关键边
            if components < k {
                // 需要移除所有 time <= edge[2] 的边
                return edge[2];
            }
        }

        // 所有边保留时连通分量仍 >= k，无需移除任何边
        0
    }
}
```
