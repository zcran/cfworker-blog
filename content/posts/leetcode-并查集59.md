---
title: "leetcode-并查集59"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 最小化连通分量的最大成本

给你一个无向连通图，包含 n 个节点，节点编号从 0 到 n - 1，以及一个二维整数数组 edges，其中 edges[i] = [ui, vi, wi] 表示一条连接节点 ui 和节点 vi 的无向边，边权为 wi，另有一个整数 k。

你可以从图中移除任意数量的边，使得最终的图中 最多 只包含 k 个连通分量。

连通分量的 成本 定义为该分量中边权的 最大值 。如果一个连通分量没有边，则其代价为 0。

请返回在移除这些边之后，在所有连通分量之中的 最大成本 的 最小可能值 。


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
    pub fn min_cost(n: i32, mut edges: Vec<Vec<i32>>, k: i32) -> i32 {
        let n = n as usize;
        let k = k as usize;

        // 如果目标连通分量数等于节点数，无需任何边，最大成本为 0
        if k == n {
            return 0;
        }

        // 按边权升序排序
        edges.sort_unstable_by(|a, b| a[2].cmp(&b[2]));

        let mut uf = UnionFind::new(n);

        // 从小到大添加边，直到连通分量数减少到 k
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let w = edge[2];

            uf.union(u, v);

            // 当连通分量数达到 k 时，当前边权即为最小可能的最大成本
            if uf.count == k {
                return w;
            }
        }

        // 理论上图是连通的，一定能达到 k 个连通分量（k >= 1）
        unreachable!()
    }
}
```
