---
title: "leetcode-数论9"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 带阈值的图连通性

有 n 座城市，编号从 1 到 n 。编号为 x 和 y 的两座城市直接连通的前提是： x 和 y 的公因数中，至少有一个 严格大于 某个阈值 threshold 。更正式地说，如果存在整数 z ，且满足以下所有条件，则编号 x 和 y 的城市之间有一条道路：

· x % z == 0
· y % z == 0
· z > threshold

给你两个整数 n 和 threshold ，以及一个待查询数组，请你判断每个查询 queries[i] = [ai, bi] 指向的城市 ai 和 bi 是否连通（即，它们之间是否存在一条路径）。

返回数组 answer ，其中answer.length == queries.length 。如果第 i 个查询中指向的城市 ai 和 bi 连通，则 answer[i] 为 true ；如果不连通，则 answer[i] 为 false 。


```
/// 并查集：维护节点的连通性
struct UnionFind {
    parent: Vec<usize>,
    size: Vec<usize>,
}

impl UnionFind {
    /// 初始化并查集，每个节点独立成组
    fn new(n: usize) -> Self {
        Self {
            parent: (0..n).collect(),
            size: vec![1; n],
        }
    }

    /// 查找根节点（路径压缩）
    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    /// 合并两个节点所在的集合
    fn union(&mut self, a: usize, b: usize) {
        let ra = self.find(a);
        let rb = self.find(b);
        if ra == rb {
            return;
        }

        // 按大小合并：将小树合并到大树
        if self.size[ra] < self.size[rb] {
            self.parent[ra] = rb;
            self.size[rb] += self.size[ra];
        } else {
            self.parent[rb] = ra;
            self.size[ra] += self.size[rb];
        }
    }

    /// 判断两个节点是否连通
    fn connected(&mut self, a: usize, b: usize) -> bool {
        self.find(a) == self.find(b)
    }
}

impl Solution {
    /// 判断城市之间是否连通
    ///
    /// 核心思路：
    /// 1. 如果两个城市有共同的质因数 z > threshold，则它们连通
    /// 2. 对每个 z > threshold，将 z 的所有倍数连接起来
    /// 3. 使用并查集维护连通性
    pub fn are_connected(n: i32, threshold: i32, queries: Vec<Vec<i32>>) -> Vec<bool> {
        let n = n as usize;
        let threshold = threshold as usize;
        let mut uf = UnionFind::new(n + 1); // 城市编号从 1 到 n

        // ---------- 1. 连接所有共享公因数 > threshold 的城市 ----------
        // 枚举所有可能的公因数 z
        for z in (threshold + 1)..=n {
            // 将 z 的所有倍数连接在一起
            // p 是第一个倍数，q 是第二个倍数
            // 将 p 和 q 连接，相当于将所有倍数连接成一条链
            let mut p = z;
            let mut q = z * 2;
            while q <= n {
                uf.union(p, q);
                p += z;
                q += z;
            }
        }

        // ---------- 2. 处理查询 ----------
        queries
            .into_iter()
            .map(|q| uf.connected(q[0] as usize, q[1] as usize))
            .collect()
    }
}
```
