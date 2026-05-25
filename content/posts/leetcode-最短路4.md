---
title: "leetcode-最短路4"
date: 2026-04-22T20:45:19+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 细分图中的可到达节点

给你一个无向图（原始图），图中有 n 个节点，编号从 0 到 n - 1 。你决定将图中的每条边 细分 为一条节点链，每条边之间的新节点数各不相同。

图用由边组成的二维数组 edges 表示，其中 edges[i] = [ui, vi, cnti] 表示原始图中节点 ui 和 vi 之间存在一条边，cnti 是将边 细分 后的新节点总数。注意，cnti == 0 表示边不可细分。

要 细分 边 [ui, vi] ，需要将其替换为 (cnti + 1) 条新边，和 cnti 个新节点。新节点为 x1, x2, ..., xcnti ，新边为 [ui, x1], [x1, x2], [x2, x3], ..., [xcnti-1, xcnti], [xcnti, vi] 。

现在得到一个 新的细分图 ，请你计算从节点 0 出发，可以到达多少个节点？如果节点间距离是 maxMoves 或更少，则视为 可以到达 。

给你原始图和 maxMoves ，返回 新的细分图中从节点 0 出发 可到达的节点数 。


```
use std::cmp::Reverse;      // 用于将 BinaryHeap 变为最小堆
use std::collections::BinaryHeap;

impl Solution {
    /// 计算在最多移动 max_moves 步的情况下，可以从节点 0 到达的节点总数。
    /// 原图每条边 (u, v, cnt) 表示 u 和 v 之间原本有 cnt 个细分节点（加上两端共 cnt+2 个点），
    /// 但移动时可以在这些细分节点上停留。函数返回可达的原节点与细分节点总数。
    ///
    /// # 参数
    /// - `edges`: 边列表，每条边为 [u, v, cnt]，cnt 是 u 和 v 之间细分节点的数量。
    /// - `max_moves`: 允许的最大移动步数（每移动一次走到一个相邻节点）。
    /// - `n`: 原始节点数量（编号 0..n-1）。
    pub fn reachable_nodes(edges: Vec<Vec<i32>>, max_moves: i32, n: i32) -> i32 {
        // ---------- 1. 构建邻接表 ----------
        // graph[u] 存储 (邻接节点 v, 边上的细分节点个数)
        let mut graph = vec![vec![]; n as usize];
        for edge in &edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let cnt = edge[2] as usize;   // 细分节点个数
            graph[u].push((v, cnt));
            graph[v].push((u, cnt));
        }

        // ---------- 2. Dijkstra 算法，计算从节点 0 到每个原节点的最短路径长度 ----------
        // dist[i] 表示从 0 到 i 需要的最少移动步数（经过的节点数减一，即边数）
        let mut dist = vec![usize::MAX; n as usize];
        let mut heap = BinaryHeap::new();   // 最小堆存储 (距离, 节点)
        dist[0] = 0;
        heap.push(Reverse((0, 0)));         // (距离, 节点)

        while let Some(Reverse((d, u))) = heap.pop() {
            // 如果堆中弹出的距离不是当前记录的最短距离，则跳过（过时条目）
            if d != dist[u] {
                continue;
            }
            // 遍历所有邻接边
            for &(v, cnt) in &graph[u] {
                // 从 u 走到 v 需要经过 cnt 个细分节点，加上 u->v 原边本来就要 1 步到达 v，
                // 所以总步数为 d + cnt + 1
                let new_d = d + cnt + 1;
                if new_d < dist[v] {
                    dist[v] = new_d;
                    heap.push(Reverse((new_d, v)));
                }
            }
        }

        let max_moves = max_moves as usize;

        // ---------- 3. 统计所有可达的原节点 ----------
        // 只要 dist[node] <= max_moves，该原节点即可到达
        let mut ans = dist.iter().filter(|&&d| d <= max_moves).count();

        // ---------- 4. 统计每条边上可到达的细分节点数量 ----------
        // 对于边 (u, v, cnt)，已经知道从 0 到 u 和到 v 的最短距离 dist[u] 和 dist[v]。
        // 从 u 出发最多还能走 max_moves - dist[u] 步，从 v 出发最多还能走 max_moves - dist[v] 步。
        // 这条边上的细分节点可以被从两端走近，但重复部分只算一次，且不能超过 cnt。
        // 因此该边上可达的细分节点数为 min(cnt, 从 u 端剩余步数 + 从 v 端剩余步数)。
        // 注意 saturating_sub 防止下溢（若 dist > max_moves 则剩余为 0）。
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let cnt = edge[2] as usize;

            let remain_u = max_moves.saturating_sub(dist[u]); // 从 u 出发还可走的步数
            let remain_v = max_moves.saturating_sub(dist[v]); // 从 v 出发还可走的步数
            let covered = remain_u + remain_v;
            // 该边上的细分节点最多可达 cnt 个
            ans += cnt.min(covered);
        }

        ans as i32
    }
}
```
