---
title: "leetcode-拓扑排序3"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 最小高度树

树是一个无向图，其中任何两个顶点只通过一条路径连接。 换句话说，任何一个没有简单环路的连通图都是一棵树。

给你一棵包含 n 个节点的树，标记为 0 到 n - 1 。给定数字 n 和一个有 n - 1 条无向边的 edges 列表（每一个边都是一对标签），其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条无向边。

可选择树中任何一个节点作为根。当选择节点 x 作为根节点时，设结果树的高度为 h 。在所有可能的树中，具有最小高度的树（即，min(h)）被称为 最小高度树 。

请你找到所有的 最小高度树 并按 任意顺序 返回它们的根节点标签列表。

树的 高度 是指根节点和叶子节点之间最长向下路径上边的数量。

```
use std::collections::VecDeque;

impl Solution {
    /// 寻找所有最小高度树的根节点标签
    ///
    /// # 参数
    /// - `n`: 节点个数，标签从 0 到 n-1
    /// - `edges`: 无向边列表，每条边由 `[u, v]` 表示
    ///
    /// # 返回
    /// - 包含所有最小高度树根节点标签的向量（顺序任意）
    ///
    /// # 算法思想
    /// 最小高度树的根位于树的**中心**位置。可以通过反复删除当前所有叶子节点（度数为1的节点），
    /// 每次删除后剩余节点的度数相应减少，直至剩余节点数量 ≤ 2。此时剩下的节点即为答案。
    ///
    /// 该方法等价于寻找图的**直径中点**，且保证正确。
    pub fn find_min_height_trees(n: i32, edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n_usize = n as usize;

        // 处理单节点的退化情形
        if n == 1 {
            return vec![0];
        }

        // ---------- 1. 构建邻接表与度数数组 ----------
        let mut degree = vec![0; n_usize];
        let mut adj = vec![vec![]; n_usize];

        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            degree[u] += 1;
            degree[v] += 1;
            adj[u].push(v);
            adj[v].push(u);
        }

        // ---------- 2. 初始化队列（所有叶子节点） ----------
        // 函数式：使用 filter 与 collect 直接构建队列，无需单独循环
        let mut queue: VecDeque<usize> = (0..n_usize)
            .filter(|&i| degree[i] == 1)
            .collect();

        let mut remaining = n_usize;   // 当前剩余节点数量

        // ---------- 3. 循环剥离叶子节点 ----------
        while remaining > 2 {
            // 每一轮移除当前所有叶子节点
            let leaf_count = queue.len();
            remaining -= leaf_count;

            // 对当前层的每个叶子节点进行处理
            for _ in 0..leaf_count {
                let leaf = queue.pop_front().unwrap();
                // 遍历邻居：删除 leaf 会导致邻居度数减 1
                for &neighbor in &adj[leaf] {
                    degree[neighbor] -= 1;
                    if degree[neighbor] == 1 {
                        // 该邻居变成新的叶子，加入下一轮队列
                        queue.push_back(neighbor);
                    }
                }
            }
        }

        // ---------- 4. 返回剩余节点（转为 i32 格式） ----------
        queue.into_iter().map(|node| node as i32).collect()
    }
}
```
