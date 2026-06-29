---
title: "leetcode-并查集34"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 检查边长度限制的路径是否存在

给你一个 n 个点组成的无向图边集 edgeList ，其中 edgeList[i] = [ui, vi, disi] 表示点 ui 和点 vi 之间有一条长度为 disi 的边。请注意，两个点之间可能有 超过一条边 。

给你一个查询数组queries ，其中 queries[j] = [pj, qj, limitj] ，你的任务是对于每个查询 queries[j] ，判断是否存在从 pj 到 qj 的路径，且这条路径上的每一条边都 严格小于 limitj 。

请你返回一个 布尔数组 answer ，其中 answer.length == queries.length ，当 queries[j] 的查询结果为 true 时， answer 第 j 个值为 true ，否则为 false 。


```
impl Solution {
    pub fn distance_limited_paths_exist(n: i32, mut edge_list: Vec<Vec<i32>>, mut queries: Vec<Vec<i32>>) -> Vec<bool> {
        // 并查集结构
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

            fn union(&mut self, a: usize, b: usize) {
                let ra = self.find(a);
                let rb = self.find(b);
                if ra == rb {
                    return;
                }
                // 按秩合并，优化性能
                if self.rank[ra] < self.rank[rb] {
                    self.parent[ra] = rb;
                } else if self.rank[ra] > self.rank[rb] {
                    self.parent[rb] = ra;
                } else {
                    self.parent[rb] = ra;
                    self.rank[ra] += 1;
                }
            }
        }

        let q_len = queries.len();

        // 为每个查询记录原始索引，便于最后按序输出
        for (i, q) in queries.iter_mut().enumerate() {
            q.push(i as i32);
        }

        // 按边权升序排列
        edge_list.sort_unstable_by_key(|e| e[2]);
        // 按限制升序排列
        queries.sort_unstable_by_key(|q| q[2]);

        let mut dsu = DSU::new(n as usize);
        let mut edge_idx = 0;
        let mut result = vec![false; q_len];

        for q in queries {
            let limit = q[2];
            let u = q[0] as usize;
            let v = q[1] as usize;
            let orig_idx = q[3] as usize;

            // 将所有边权 < limit 的边加入并查集
            while edge_idx < edge_list.len() && edge_list[edge_idx][2] < limit {
                let a = edge_list[edge_idx][0] as usize;
                let b = edge_list[edge_idx][1] as usize;
                dsu.union(a, b);
                edge_idx += 1;
            }

            // 检查 u 和 v 是否连通
            result[orig_idx] = dsu.find(u) == dsu.find(v);
        }

        result
    }
}
```
