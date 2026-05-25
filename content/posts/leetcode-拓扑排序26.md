---
title: "leetcode-拓扑排序26"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


##  追逐游戏

秋游中的小力和小扣设计了一个追逐游戏。他们选了秋日市集景区中的 N 个景点，景点编号为 1~N。此外，他们还选择了 N 条小路，满足任意两个景点之间都可以通过小路互相到达，且不存在两条连接景点相同的小路。整个游戏场景可视作一个无向连通图，记作二维数组 edges，数组中以 [a,b] 形式表示景点 a 与景点 b 之间有一条小路连通。

小力和小扣只能沿景点间的小路移动。小力的目标是在最快时间内追到小扣，小扣的目标是尽可能延后被小力追到的时间。游戏开始前，两人分别站在两个不同的景点 startA 和 startB。每一回合，小力先行动，小扣观察到小力的行动后再行动。小力和小扣在每回合可选择以下行动之一：

移动至相邻景点
留在原地
如果小力追到小扣（即两人于某一时刻出现在同一位置），则游戏结束。若小力可以追到小扣，请返回最少需要多少回合；若小力无法追到小扣，请返回 -1。

注意：小力和小扣一定会采取最优移动策略。


```
use std::cmp::{max, min};
use std::collections::VecDeque;

const INF: i32 = 1e9 as i32;

impl Solution {
    /// 返回 A 能抓到 B 的最大步数，若永远抓不到则返回 -1。
    /// 算法：
    /// 1. 建图，计算度数，拓扑排序删去所有树枝，得到环上节点。
    /// 2. BFS 计算 A 和 B 到所有节点的最短距离。
    /// 3. 若环的大小 > 3，判断 B 是否能永远逃脱（在环上或存在环上节点使 A 追不上）。
    /// 4. 从 B 出发 BFS，找出所有 B 能安全到达（A 无法及时抓住）的节点，取最大到达步数。
    pub fn chase_game(edges: Vec<Vec<i32>>, a: i32, b: i32) -> i32 {
        // ---------- 0. 节点编号从 0 开始，确定节点总数 ----------
        let n = edges
            .iter()
            .flat_map(|e| e.iter().map(|&x| x as usize))
            .max()
            .unwrap_or(0)
            + 1;
        let a = a as usize;
        let b = b as usize;

        // ---------- 1. 建图，统计度数 ----------
        let mut graph = vec![vec![]; n];
        let mut degree = vec![0; n];
        for e in &edges {
            let x = e[0] as usize;
            let y = e[1] as usize;
            graph[x].push(y);
            graph[y].push(x);
            degree[x] += 1;
            degree[y] += 1;
        }

        // 特判：A 一步就能抓到 B
        if graph[a].contains(&b) {
            return 1;
        }

        // ---------- 2. 拓扑排序，找出环上节点 ----------
        let mut ring = degree.clone(); // 拷贝度数，用于剪枝
        let mut q = VecDeque::new();
        for i in 0..n {
            if ring[i] == 1 {
                q.push_back(i);
            }
        }
        while let Some(x) = q.pop_front() {
            ring[x] -= 1; // 标记为已删除
            for &y in &graph[x] {
                if ring[y] > 0 {
                    ring[y] -= 1;
                    if ring[y] == 1 {
                        q.push_back(y);
                    }
                }
            }
        }
        // 此时 ring[i] == 2 表示该节点在环上（原代码逻辑）⚠️ 注意：度数可能 >2，这里保持原判定
        let on_ring = |i: usize| ring[i] == 2;

        // ---------- 3. BFS 求 A 和 B 的最短距离 ----------
        let mut dist_a = vec![INF; n];
        let mut dist_b = vec![INF; n];
        bfs(a, &graph, &mut dist_a);
        bfs(b, &graph, &mut dist_b);

        // ---------- 4. 若环的大小 > 3，判断 B 是否能永远逃脱 ----------
        let ring_size = (0..n).filter(|&i| on_ring(i)).count();
        if ring_size > 3 {
            // B 一开始就在环上，或存在环上节点 i 满足 A 无法及时赶到
            if on_ring(b) {
                return -1;
            }
            for i in 0..n {
                if on_ring(i) && dist_a[i] - 1 > dist_b[i] {
                    return -1;
                }
            }
        }

        // ---------- 5. 从 B 出发 BFS，找所有安全节点，取最大 dist_a ----------
        let mut visited = vec![false; n];
        let mut q = VecDeque::new();
        q.push_back(b);
        visited[b] = true;
        let mut res = dist_a[b];

        while let Some(x) = q.pop_front() {
            for &y in &graph[x] {
                if visited[y] {
                    continue;
                }
                // 条件：A 无法在 B 到达 y 之前抓到 B（A 需要 dist_a[y] 步，B 需要 dist_b[y] 步）
                if dist_a[y] - 1 > dist_b[y] {
                    // B 能安全到达 y
                    res = max(res, dist_a[y]);
                    visited[y] = true;
                    q.push_back(y);
                } else {
                    // 不能安全到达，但 A 到达 y 的步数仍可能更新答案
                    res = max(res, dist_a[y]);
                }
            }
        }

        res
    }
}

/// 从起点 src 开始 BFS，计算到所有节点的最短距离
fn bfs(src: usize, graph: &[Vec<usize>], dist: &mut [i32]) {
    let mut q = VecDeque::new();
    q.push_back(src);
    dist[src] = 0;
    while let Some(x) = q.pop_front() {
        let d = dist[x];
        for &y in &graph[x] {
            if dist[y] > d + 1 {
                dist[y] = d + 1;
                q.push_back(y);
            }
        }
    }
}
```
