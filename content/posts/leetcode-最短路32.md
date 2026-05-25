---
title: "leetcode-最短路32"
date: 2026-04-22T20:45:21+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 除法求值


给定一个变量对数组 equations 和一个实数值数组 values 作为已知条件，其中 equations[i] = [Ai, Bi] 和 values[i] 共同表示等式 Ai / Bi = values[i] 。每个 Ai 或 Bi 是一个表示单个变量的字符串。

另有一些以数组 queries 表示的问题，其中 queries[j] = [Cj, Dj] 表示第 j 个问题，请你根据已知条件找出 Cj / Dj = ? 的结果作为答案。

返回 所有问题的答案 。如果存在某个无法确定的答案，则用 -1.0 替代这个答案。如果问题中出现了给定的已知条件中没有出现的字符串，也需要用 -1.0 替代这个答案。

注意：输入总是有效的。可以假设除法运算中不会出现除数为 0 的情况，且不存在任何矛盾的结果。

```
use std::collections::HashMap;

impl Solution {
    /// 计算给定等式组的变量比值。
    ///
    /// # 参数
    /// - `equations`: 形如 [["a", "b"], ["b", "c"]] 的等式，表示 a/b = values[i]
    /// - `values`: 每个等式的比值
    /// - `queries`: 形如 [["a", "c"], ["x", "y"]] 的查询，返回 a/c 的值（若无法确定则 -1.0）
    ///
    /// # 返回
    /// 每个查询的结果向量
    pub fn calc_equation(
        equations: Vec<Vec<String>>,
        values: Vec<f64>,
        queries: Vec<Vec<String>>,
    ) -> Vec<f64> {
        // ---------- 1. 为所有变量分配唯一 ID ----------
        let mut var_to_id = HashMap::new();
        let mut next_id = 0;
        for eq in &equations {
            for var in &[&eq[0], &eq[1]] {
                var_to_id.entry(var.clone()).or_insert_with(|| {
                    let id = next_id;
                    next_id += 1;
                    id
                });
            }
        }
        let n = next_id as usize; // 变量总数

        // ---------- 2. 初始化并查集 ----------
        // parent[i] = i 的父节点（根节点指向自身）
        let mut parent = (0..n).collect::<Vec<_>>();
        // weight[i] = 变量 i 到其父节点的比值（即 i / parent[i]）
        let mut weight = vec![1.0; n];

        // 带权并查集的查找（路径压缩，同时更新权重）
        fn find(parent: &mut Vec<usize>, weight: &mut Vec<f64>, x: usize) -> usize {
            if parent[x] != x {
                let root = find(parent, weight, parent[x]);
                // 更新权重：x -> root = (x -> parent[x]) * (parent[x] -> root)
                weight[x] *= weight[parent[x]];
                parent[x] = root;
            }
            parent[x]
        }

        // 合并两个变量，已知 a / b = val
        fn union(
            parent: &mut Vec<usize>,
            weight: &mut Vec<f64>,
            a: usize,
            b: usize,
            val: f64,
        ) {
            let ra = find(parent, weight, a);
            let rb = find(parent, weight, b);
            if ra == rb {
                return; // 已经连通，无需合并（但可根据需要检查一致性）
            }
            // 将 ra 的父节点设为 rb，并更新 weight[ra] 使得满足 a / b = val
            // 推导：已知 weight[a] = a/ra, weight[b] = b/rb，要满足 (a/ra) / (b/rb) * (ra/rb) = val
            // 即 weight[ra] = val * weight[b] / weight[a]
            parent[ra] = rb;
            weight[ra] = val * weight[b] / weight[a];
        }

        // ---------- 3. 合并所有给出的等式 ----------
        for (eq, &val) in equations.iter().zip(values.iter()) {
            let a = var_to_id[&eq[0]];
            let b = var_to_id[&eq[1]];
            union(&mut parent, &mut weight, a, b, val);
        }

        // ---------- 4. 回答每个查询 ----------
        queries
            .into_iter()
            .map(|q| {
                let (var_a, var_b) = (&q[0], &q[1]);
                match (var_to_id.get(var_a), var_to_id.get(var_b)) {
                    (Some(&a), Some(&b)) => {
                        // 先找到根（同时完成路径压缩和权重更新）
                        let ra = find(&mut parent, &mut weight, a);
                        let rb = find(&mut parent, &mut weight, b);
                        if ra == rb {
                            // a / b = (a / ra) / (b / ra) = weight[a] / weight[b]
                            weight[a] / weight[b]
                        } else {
                            -1.0
                        }
                    }
                    _ => -1.0, // 至少一个变量未出现过
                }
            })
            .collect()
    }
}
```
