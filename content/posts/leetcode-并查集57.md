---
title: "leetcode-并查集57"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 升级后最大生成树稳定性

给你一个整数 n，表示编号从 0 到 n - 1 的 n 个节点，以及一个 edges 列表，其中 edges[i] = [ui, vi, si, musti]：

· ui 和 vi 表示节点 ui 和 vi 之间的一条无向边。
· si 是该边的强度。
· musti 是一个整数（0 或 1）。如果 musti == 1，则该边 必须 包含在生成树中，且 不能升级 。

你还有一个整数 k，表示你可以执行的最多 升级 次数。每次升级会使边的强度 翻倍 ，且每条可升级边（即 musti == 0）最多只能升级一次。

一个生成树的 稳定性 定义为其中所有边的 最小 强度。

返回任何有效生成树可能达到的 最大 稳定性。如果无法连接所有节点，返回 -1。

注意： 图的一个 生成树（spanning tree）是该图中边的一个子集，它满足以下条件：

· 将所有节点连接在一起（即图是 连通的 ）。
· 不 形成任何环。
· 包含 恰好 n - 1 条边，其中 n 是图中节点的数量。


```
struct DSU {
    parent: Vec<usize>,
    rank: Vec<usize>,
}

impl DSU {
    fn new(n: usize) -> Self {
        Self {
            parent: (0..n).collect(),
            rank: vec![0; n],
        }
    }

    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    fn union(&mut self, x: usize, y: usize) -> bool {
        let rx = self.find(x);
        let ry = self.find(y);
        if rx == ry {
            return false;
        }
        // 按秩合并
        if self.rank[rx] < self.rank[ry] {
            self.parent[rx] = ry;
        } else {
            self.parent[ry] = rx;
            if self.rank[rx] == self.rank[ry] {
                self.rank[rx] += 1;
            }
        }
        true
    }
}

impl Solution {
    pub fn max_stability(n: i32, edges: Vec<Vec<i32>>, k: i32) -> i32 {
        let n = n as usize;
        let k = k as usize;

        // 边数不足，无法生成树
        if edges.len() < n - 1 {
            return -1;
        }

        // 分离必须边和可选边
        let mut must_edges = Vec::new();
        let mut optional_edges = Vec::new();

        for e in edges {
            let (u, v, w, must) = (e[0] as usize, e[1] as usize, e[2], e[3]);
            if must == 1 {
                must_edges.push((u, v, w));
            } else {
                optional_edges.push((u, v, w));
            }
        }

        // 必须边过多，无法生成树
        if must_edges.len() > n - 1 {
            return -1;
        }

        // 先连接所有必须边
        let mut dsu = DSU::new(n);
        let mut edge_count = 0;
        let mut min_stability = i32::MAX;

        for &(u, v, w) in &must_edges {
            if !dsu.union(u, v) {
                return -1; // 必须边成环
            }
            edge_count += 1;
            min_stability = min_stability.min(w);
        }

        // 按强度降序排列可选边
        optional_edges.sort_unstable_by(|a, b| b.2.cmp(&a.2));

        // 二分查找最大稳定性
        let mut left = 0;
        let mut right = min_stability;
        let mut answer = -1;

        while left <= right {
            let mid = left + (right - left) / 2;

            // 尝试构造稳定性 >= mid 的生成树
            let mut dsu_clone = DSU::new(n);
            // 克隆并查集状态（仅需复制必须边连接状态）
            for i in 0..n {
                dsu_clone.parent[i] = dsu.parent[i];
                dsu_clone.rank[i] = dsu.rank[i];
            }

            let mut count = edge_count;
            let mut upgrades = 0;

            for &(u, v, w) in &optional_edges {
                if count == n - 1 {
                    break;
                }
                if dsu_clone.find(u) == dsu_clone.find(v) {
                    continue;
                }

                if w >= mid {
                    // 直接使用该边
                    dsu_clone.union(u, v);
                    count += 1;
                } else if upgrades < k && w * 2 >= mid {
                    // 升级后使用
                    dsu_clone.union(u, v);
                    count += 1;
                    upgrades += 1;
                }
            }

            if count == n - 1 {
                // 可以构造，尝试更大稳定性
                answer = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        answer
    }
}
```
