---
title: "leetcode-并查集31"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 保证图可完全遍历


Alice 和 Bob 共有一个无向图，其中包含 n 个节点和 3  种类型的边：

· 类型 1：只能由 Alice 遍历。
· 类型 2：只能由 Bob 遍历。
· 类型 3：Alice 和 Bob 都可以遍历。

给你一个数组 edges ，其中 edges[i] = [typei, ui, vi] 表示节点 ui 和 vi 之间存在类型为 typei 的双向边。请你在保证图仍能够被 Alice和 Bob 完全遍历的前提下，找出可以删除的最大边数。如果从任何节点开始，Alice 和 Bob 都可以到达所有其他节点，则认为图是可以完全遍历的。

返回可以删除的最大边数，如果 Alice 和 Bob 无法完全遍历图，则返回 -1 。

```
impl Solution {
    /// 返回可以删除的最大边数，使得 Alice 和 Bob 仍能完全遍历图。
    /// 优先保留类型 3 边（公用），再处理独占边。
    pub fn max_num_edges_to_remove(n: i32, edges: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;
        let mut alice = DSU::new(n);
        let mut bob = DSU::new(n);
        let mut removable = 0;

        // 1. 处理类型 3 边（公共边）
        for edge in edges.iter().filter(|e| e[0] == 3) {
            let u = (edge[1] - 1) as usize;
            let v = (edge[2] - 1) as usize;
            let a_ok = alice.union(u, v);
            let b_ok = bob.union(u, v);
            if !a_ok && !b_ok {
                removable += 1; // 对双方都冗余，可删除
            }
        }

        // 2. 处理类型 1 和 2 边（独占边）
        for edge in edges {
            let u = (edge[1] - 1) as usize;
            let v = (edge[2] - 1) as usize;
            match edge[0] {
                1 => {
                    if !alice.union(u, v) {
                        removable += 1;
                    }
                }
                2 => {
                    if !bob.union(u, v) {
                        removable += 1;
                    }
                }
                _ => {} // 类型 3 已处理
            }
        }

        // 检查是否完全连通
        if alice.count == 1 && bob.count == 1 {
            removable
        } else {
            -1
        }
    }
}

/// 并查集（路径压缩 + 按秩合并）
struct DSU {
    parent: Vec<usize>,
    rank: Vec<usize>,
    count: usize, // 连通分量个数
}

impl DSU {
    fn new(n: usize) -> Self {
        DSU {
            parent: (0..n).collect(),
            rank: vec![0; n],
            count: n,
        }
    }

    /// 查找根节点（路径压缩，迭代）
    fn find(&mut self, mut x: usize) -> usize {
        while self.parent[x] != x {
            self.parent[x] = self.parent[self.parent[x]];
            x = self.parent[x];
        }
        x
    }

    /// 合并两个节点，返回是否成功（即是否原本不连通）
    fn union(&mut self, x: usize, y: usize) -> bool {
        let rx = self.find(x);
        let ry = self.find(y);
        if rx == ry {
            return false;
        }
        // 按秩合并
        if self.rank[rx] < self.rank[ry] {
            self.parent[rx] = ry;
        } else if self.rank[rx] > self.rank[ry] {
            self.parent[ry] = rx;
        } else {
            self.parent[ry] = rx;
            self.rank[rx] += 1;
        }
        self.count -= 1;
        true
    }
}
```
