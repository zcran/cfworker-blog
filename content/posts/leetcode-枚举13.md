---
title: "leetcode-枚举13"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 一个图中连通三元组的最小度数

给你一个无向图，整数 n 表示图中节点的数目，edges 数组表示图中的边，其中 edges[i] = [ui, vi] ，表示 ui 和 vi 之间有一条无向边。

一个 连通三元组 指的是 三个 节点组成的集合且这三个点之间 两两 有边。

连通三元组的度数 是所有满足此条件的边的数目：一个顶点在这个三元组内，而另一个顶点不在这个三元组内。

请你返回所有连通三元组中度数的 最小值 ，如果图中没有连通三元组，那么返回 -1 。


```
impl Solution {
    /// 返回所有连通三元组的最小度数，如果不存在连通三元组则返回 -1。
    ///
    /// 连通三元组：三个节点两两之间都有边相连。
    /// 三元组的度数：三元组内节点到外部节点的边数 = 三个节点的度之和 - 6
    /// （因为三元组内部有 3 条边，贡献了 6 个度）。
    pub fn min_trio_degree(n: i32, edges: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 邻接矩阵，g[u][v] = true 表示 u 和 v 之间有边
        let mut is_connected = vec![vec![false; n]; n];
        // 每个节点的度数
        let mut degree = vec![0; n];

        // 建图：填充邻接矩阵并统计度数
        for edge in edges {
            let u = (edge[0] - 1) as usize;
            let v = (edge[1] - 1) as usize;
            is_connected[u][v] = true;
            is_connected[v][u] = true;
            degree[u] += 1;
            degree[v] += 1;
        }

        let mut min_degree = i32::MAX;

        // 枚举所有可能的三元组 (i, j, k)，其中 i < j < k
        for i in 0..n {
            for j in (i + 1)..n {
                // 剪枝：如果 i 和 j 不相连，则不可能构成三元组
                if !is_connected[i][j] {
                    continue;
                }

                for k in (j + 1)..n {
                    // 检查是否构成连通三元组：i-j、i-k、j-k 都相连
                    if is_connected[i][k] && is_connected[j][k] {
                        // 计算当前三元组的度数并更新最小值
                        let current_degree = degree[i] + degree[j] + degree[k] - 6;
                        if current_degree < min_degree {
                            min_degree = current_degree;
                        }
                    }
                }
            }
        }

        // 如果没有找到任何连通三元组，返回 -1
        if min_degree == i32::MAX {
            -1
        } else {
            min_degree
        }
    }
}
```
