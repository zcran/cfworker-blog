---
title: "leetcode-并查集55"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 属性图

给你一个二维整数数组 properties，其维度为 n x m，以及一个整数 k。

定义一个函数 intersect(a, b)，它返回数组 a 和 b 中 共有的不同整数的数量 。

构造一个 无向图，其中每个索引 i 对应 properties[i]。如果且仅当 intersect(properties[i], properties[j]) >= k（其中 i 和 j 的范围为 [0, n - 1] 且 i != j），节点 i 和节点 j 之间有一条边。

返回结果图中 连通分量 的数量。


```
use std::collections::HashSet;

struct UnionFind {
    parent: Vec<usize>,
    rank: Vec<usize>,
    count: i32, // 连通分量数量
}

impl UnionFind {
    fn new(n: usize) -> Self {
        Self {
            parent: (0..n).collect(),
            rank: vec![0; n],
            count: n as i32,
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
    pub fn number_of_components(properties: Vec<Vec<i32>>, k: i32) -> i32 {
        let n = properties.len();
        if n == 0 {
            return 0;
        }

        // 将每个属性数组转换为 HashSet 以去重并支持快速查找
        let sets: Vec<HashSet<i32>> = properties
            .iter()
            .map(|arr| arr.iter().copied().collect())
            .collect();

        let mut uf = UnionFind::new(n);

        // 枚举所有节点对，如果交集大小 >= k 则合并
        for i in 0..n {
            for j in 0..i {
                // 选择较小的集合进行遍历，优化性能
                let (small, large) = if sets[i].len() < sets[j].len() {
                    (&sets[i], &sets[j])
                } else {
                    (&sets[j], &sets[i])
                };

                let mut cnt = 0;
                for &val in small {
                    if large.contains(&val) {
                        cnt += 1;
                        // 提前终止，因为只需判断是否达到 k
                        if cnt >= k {
                            break;
                        }
                    }
                }
                if cnt >= k {
                    uf.union(i, j);
                }
            }
        }

        uf.count
    }
}
```
