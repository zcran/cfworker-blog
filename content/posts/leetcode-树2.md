---
title: "leetcode-树2"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 最大价值和与最小价值和的差值

给你一个 n 个节点的无向无根图，节点编号为 0 到 n - 1 。给你一个整数 n 和一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间有一条边。

每个节点都有一个价值。给你一个整数数组 price ，其中 price[i] 是第 i 个节点的价值。

一条路径的 价值和 是这条路径上所有节点的价值之和。

你可以选择树中任意一个节点作为根节点 root 。选择 root 为根的 开销 是以 root 为起点的所有路径中，价值和 最大的一条路径与最小的一条路径的差值。

请你返回所有节点作为根节点的选择中，最大 的 开销 为多少。


```
impl Solution {
    pub fn max_output(n: i32, edges: Vec<Vec<i32>>, price: Vec<i32>) -> i64 {
        let n = n as usize;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (x, y) = (edge[0] as usize, edge[1] as usize);
            graph[x].push(y);
            graph[y].push(x);
        }

        let mut ans = 0i64;

        // DFS 返回 (以 x 为起点、且路径末端为“带叶子的路径”的最大和,
        //           以 x 为起点、且路径末端为“不带叶子的路径”的最大和)
        //
        // 带叶子：路径末端是原树中的叶子节点（在 root 视角下可延伸到叶子）
        // 不带叶子：路径末端可以是任意节点，但该路径在 root 视角下不延伸到叶子
        fn dfs(
            x: usize,
            parent: usize,
            graph: &[Vec<usize>],
            price: &[i32],
            ans: &mut i64,
        ) -> (i64, i64) {
            let p = price[x] as i64;

            // max_with_leaf:   以 x 为端点、末端为叶子的最大路径和
            // max_without_leaf: 以 x 为端点、末端不为叶子的最大路径和
            let mut max_with_leaf = p;
            let mut max_without_leaf = 0;

            for &y in &graph[x] {
                if y == parent {
                    continue;
                }

                let (child_with_leaf, child_without_leaf) = dfs(y, x, graph, price, ans);

                // 用两条来自不同子树的路径拼接，经过 x 形成一条完整路径
                // 情况 1：前面“带叶子”路径 + 当前“不带叶子”路径
                // 情况 2：前面“不带叶子”路径 + 当前“带叶子”路径
                *ans = (*ans).max(max_with_leaf + child_without_leaf)
                    .max(max_without_leaf + child_with_leaf);

                // 更新以 x 为端点的两条最优路径
                max_with_leaf = max_with_leaf.max(child_with_leaf + p);
                max_without_leaf = max_without_leaf.max(child_without_leaf + p);
            }

            (max_with_leaf, max_without_leaf)
        }

        dfs(0, usize::MAX, &graph, &price, &mut ans);

        ans
    }
}
```
