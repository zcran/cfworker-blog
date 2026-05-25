---
title: "leetcode-最短路1"
date: 2026-04-22T20:45:19+08:00
tags: ["leetcode", "最短路"]
draft: false
---

## 除法求值


给你一个变量对数组 equations 和一个实数值数组 values 作为已知条件，其中 equations[i] = [Ai, Bi] 和 values[i] 共同表示等式 Ai / Bi = values[i] 。每个 Ai 或 Bi 是一个表示单个变量的字符串。

另有一些以数组 queries 表示的问题，其中 queries[j] = [Cj, Dj] 表示第 j 个问题，请你根据已知条件找出 Cj / Dj = ? 的结果作为答案。

返回 所有问题的答案 。如果存在某个无法确定的答案，则用 -1.0 替代这个答案。如果问题中出现了给定的已知条件中没有出现的字符串，也需要用 -1.0 替代这个答案。

注意：输入总是有效的。你可以假设除法运算中不会出现除数为 0 的情况，且不存在任何矛盾的结果。

注意：未在等式列表中出现的变量是未定义的，因此无法确定它们的答案。


```
use std::collections::HashMap;

impl Solution {
    pub fn calc_equation(
        equations: Vec<Vec<String>>,
        values: Vec<f64>,
        queries: Vec<Vec<String>>,
    ) -> Vec<f64> {
        // ---------- 并查集结构体（带权重的联合查找）----------
        struct UnionFind {
            parent: Vec<usize>, // 父节点索引
            ratio: Vec<f64>,    // ratio[i] 表示 node_i = ratio[i] * node_{parent[i]}
        }

        impl UnionFind {
            /// 初始化 n 个独立节点，每个节点的比值因子为 1.0
            fn new(n: usize) -> Self {
                Self {
                    parent: (0..n).collect(),
                    ratio: vec![1.0; n],
                }
            }

            /// 查找节点 x 的根节点，并执行路径压缩，同时更新比值因子
            /// 返回根节点索引
            fn find(&mut self, x: usize) -> usize {
                if x != self.parent[x] {
                    let orig_parent = self.parent[x];
                    let root = self.find(orig_parent);
                    // 更新当前节点的比值：x -> root 的比值 = (x -> parent) * (parent -> root)
                    self.ratio[x] *= self.ratio[orig_parent];
                    self.parent[x] = root;
                }
                self.parent[x]
            }

            /// 合并两个节点所在的集合，已知 x / y = value
            fn union(&mut self, x: usize, y: usize, value: f64) {
                let root_x = self.find(x);
                let root_y = self.find(y);
                if root_x == root_y {
                    return; // 已经在同一集合中
                }
                // 将 root_x 挂到 root_y 下，并调整比值
                // 推导：现有关系 value = x / y，而 x = ratio[x] * X_root, y = ratio[y] * Y_root
                // 设新边 root_x -> root_y 的比值为 k，则有：
                // value = (ratio[x] * X_root) / (ratio[y] * Y_root)
                // 又因为 X_root = k * Y_root，代入得 value = (ratio[x] * k * Y_root) / (ratio[y] * Y_root)
                // 所以 value = (ratio[x] * k) / ratio[y] => k = value * ratio[y] / ratio[x]
                self.parent[root_x] = root_y;
                self.ratio[root_x] = value * self.ratio[y] / self.ratio[x];
            }

            /// 计算两个变量（已映射为索引）的比值，如果不在同一集合中返回 -1.0
            fn query(&mut self, idx_x: usize, idx_y: usize) -> f64 {
                let root_x = self.find(idx_x);
                let root_y = self.find(idx_y);
                if root_x != root_y {
                    -1.0
                } else {
                    self.ratio[idx_x] / self.ratio[idx_y]
                }
            }
        }

        // ---------- 构建变量名到索引的映射 ----------
        let mut name_to_id = HashMap::new();
        let mut next_id = 0;

        // 先遍历所有方程，收集所有出现的变量并分配唯一索引
        for eq in &equations {
            for var in [&eq[0], &eq[1]] {
                if !name_to_id.contains_key(var) {
                    name_to_id.insert(var.clone(), next_id);
                    next_id += 1;
                }
            }
        }
        let var_count = next_id;

        // ---------- 初始化并查集 ----------
        let mut uf = UnionFind::new(var_count);

        // ---------- 根据方程建立关系 ----------
        for (eq, &val) in equations.iter().zip(values.iter()) {
            let id_a = name_to_id[&eq[0]];
            let id_b = name_to_id[&eq[1]];
            uf.union(id_a, id_b, val);
        }

        // ---------- 处理查询 ----------
        queries
            .into_iter()
            .map(|query| {
                let (var_a, var_b) = (&query[0], &query[1]);
                match (name_to_id.get(var_a), name_to_id.get(var_b)) {
                    (Some(&id_a), Some(&id_b)) => uf.query(id_a, id_b),
                    _ => -1.0, // 任一变量未定义
                }
            })
            .collect()
    }
}
```
