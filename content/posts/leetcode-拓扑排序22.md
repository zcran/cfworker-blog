---
title: "leetcode-拓扑排序22"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 有向图访问计数


现有一个有向图，其中包含 n 个节点，节点编号从 0 到 n - 1 。此外，该图还包含了 n 条有向边。

给你一个下标从 0 开始的数组 edges ，其中 edges[i] 表示存在一条从节点 i 到节点 edges[i] 的边。

想象在图上发生以下过程：

你从节点 x 开始，通过边访问其他节点，直到你在 此过程 中再次访问到之前已经访问过的节点。
返回数组 answer 作为答案，其中 answer[i] 表示如果从节点 i 开始执行该过程，你可以访问到的不同节点数。

```
use std::collections::VecDeque;

impl Solution {
    /// 计算每个节点能访问到的不同节点数（包含自身）。
    /// 给定一个基环内向树，每个节点出度恰好为 1。
    /// 算法：先拓扑排序剪去所有树枝，剩余节点构成一个或多个环；
    /// 环上节点的答案就是环的大小；
    /// 树枝节点（反图上的树）的答案 = 其父节点答案 + 1。
    pub fn count_visited_nodes(g: Vec<i32>) -> Vec<i32> {
        let n = g.len();
        // 反图：rg[v] 存储所有指向 v 的节点（即原图中边的反向）
        let mut rg = vec![vec![]; n];
        let mut deg = vec![0; n];          // 入度（原图中指向该节点的边数）

        // 建图并统计入度
        for (x, &y) in g.iter().enumerate() {
            let y = y as usize;
            rg[y].push(x);
            deg[y] += 1;
        }

        // 第一步：拓扑排序，删除所有不在环上的节点（树枝）
        let mut queue: VecDeque<usize> = (0..n).filter(|&i| deg[i] == 0).collect();
        while let Some(x) = queue.pop_front() {
            let y = g[x] as usize;
            deg[y] -= 1;
            if deg[y] == 0 {
                queue.push_back(y);
            }
        }

        let mut ans = vec![0; n];

        // 第二步：处理所有环
        for start in 0..n {
            if deg[start] <= 0 {
                continue;   // 已处理过或为树枝节点
            }
            // 收集当前环上的所有节点
            let mut ring = Vec::new();
            let mut x = start;
            while deg[x] > 0 {
                deg[x] = -1;          // 标记为已处理
                ring.push(x);
                x = g[x] as usize;
            }
            let ring_size = ring.len() as i32;

            // 环上节点的答案就是环的大小
            for &v in &ring {
                ans[v] = ring_size;
            }

            // 从环出发，在反图上 BFS 遍历所有树枝节点（deg == 0）
            let mut bfs_q: VecDeque<usize> = ring.into_iter().collect();
            while let Some(v) = bfs_q.pop_front() {
                for &u in &rg[v] {
                    if deg[u] == 0 {          // 树枝节点
                        ans[u] = ans[v] + 1;
                        bfs_q.push_back(u);
                    }
                }
            }
        }

        ans
    }
}
```
