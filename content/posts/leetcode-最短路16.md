---
title: "leetcode-最短路16"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 修改图中的边权

给你一个 n 个节点的 无向带权连通 图，节点编号为 0 到 n - 1 ，再给你一个整数数组 edges ，其中 edges[i] = [ai, bi, wi] 表示节点 ai 和 bi 之间有一条边权为 wi 的边。

部分边的边权为 -1（wi = -1），其他边的边权都为 正 数（wi > 0）。

你需要将所有边权为 -1 的边都修改为范围 [1, 2 * 109] 中的 正整数 ，使得从节点 source 到节点 destination 的 最短距离 为整数 target 。如果有 多种 修改方案可以使 source 和 destination 之间的最短距离等于 target ，你可以返回任意一种方案。

如果存在使 source 到 destination 最短距离为 target 的方案，请你按任意顺序返回包含所有边的数组（包括未修改边权的边）。如果不存在这样的方案，请你返回一个 空数组 。

注意：你不能修改一开始边权为正数的边。

```
impl Solution {
    /// 修改图中权值为 -1 的边（可修改），使得从 source 到 destination 的最短路径长度等于 target。
    /// 若无法实现，返回空向量；否则返回修改后的边列表。
    pub fn modified_graph_edges(
        n: i32,
        edges: Vec<Vec<i32>>,
        source: i32,
        destination: i32,
        target: i32,
    ) -> Vec<Vec<i32>> {
        let n = n as usize;
        let source = source as usize;
        let dest = destination as usize;
        let mut edges = edges;

        // 构建邻接表：adj[u][v] = Some(边索引) 或 None
        let mut adj = vec![vec![None; n]; n];
        for (idx, edge) in edges.iter().enumerate() {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            adj[u][v] = Some(idx);
            adj[v][u] = Some(idx);
        }

        // ---------- 内部 Dijkstra 算法 ----------
        // phase = 0: 将所有未知边 (-1) 临时视为权值 1
        // phase = 1: 利用已知的 dist_to_dest (from) 动态调整未知边权，使最终路径满足 target
        // 注意：由于闭包需要修改 edges，因此必须声明为 mut
        let mut dijkstra = |phase: i32, start: usize, from_dist: &[i64]| -> Vec<i64> {
            let mut dist = vec![i64::MAX; n];
            let mut used = vec![false; n];
            dist[start] = 0;

            for _ in 0..n - 1 {
                // 选择未访问节点中距离最小的节点（函数式风格）
                let u = (0..n)
                    .filter(|&i| !used[i])
                    .min_by_key(|&i| dist[i])
                    .unwrap();

                used[u] = true;

                // 松弛所有邻居
                for v in 0..n {
                    if used[v] {
                        continue;
                    }
                    if let Some(edge_idx) = adj[u][v] {
                        let w = &mut edges[edge_idx][2];
                        let candidate = if *w != -1 {
                            // 固定边权
                            dist[u] + (*w as i64)
                        } else if phase == 0 {
                            // 第一阶段：未知边暂设为 1
                            dist[u] + 1
                        } else {
                            // 第二阶段：动态调整边权使得 dist[u] + w + from_dist[v] == target
                            let required = target as i64 - dist[u] - from_dist[v];
                            if required > 0 {
                                *w = required as i32;
                                dist[u] + required
                            } else {
                                // 无法满足目标，给一个大数避免使用
                                *w = target;
                                dist[u] + target as i64
                            }
                        };
                        if candidate < dist[v] {
                            dist[v] = candidate;
                        }
                    }
                }
            }
            dist
        };

        // 第一阶段：从 destination 出发，未知边视为 1，得到每个节点到 dest 的保守距离
        let dist_to_dest = dijkstra(0, dest, &[]);
        if dist_to_dest[source] > target as i64 {
            return vec![]; // 即使未知边都取最小值也无法达到 target
        }

        // 第二阶段：从 source 出发，动态调整未知边权，并确保最终距离等于 target
        let dist_from_source = dijkstra(1, source, &dist_to_dest);
        if dist_from_source[dest] != target as i64 {
            return vec![]; // 调整后仍达不到 target（理论上不会发生，但保留检查）
        }

        edges
    }
}
```
