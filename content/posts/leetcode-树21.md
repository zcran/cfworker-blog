---
title: "leetcode-树21"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 树中子图的最大得分

给你一个 无向树 ，它包含 n 个节点，编号从 0 到 n - 1。树由一个长度为 n - 1 的二维整数数组 edges 描述，其中 edges[i] = [ai, bi] 表示在节点 ai 和节点 bi 之间有一条边。

另给你一个长度为 n 的整数数组 good，其中 good[i] 为 1 表示第 i 个节点是好节点，为 0 表示它是坏节点。

定义 子图 的 得分 为子图中好节点的数量减去坏节点的数量。

对于每个节点 i，找到包含节点 i 的所有 连通子图 中可能的最大得分。

返回一个长度为 n 的整数数组，其中第 i 个元素是节点 i 的 最大得分 。

子图 是原图的一个子集，其顶点和边均来自原图。

连通子图 是一个子图，其中每一对顶点都可以通过该子图的边相互到达。


```
impl Solution {
    /// 换根 DP
    ///
    /// 题意：
    /// 对每个节点 i，求包含 i 的所有连通子图中的最大得分（好节点 +1，坏节点 -1）。
    ///
    /// 思路：
    /// 1. 第一次 DFS（以 0 为根）：计算 down[u] —— 在以 u 为根的子树中，
    ///    包含 u 的连通子图的最大得分。
    ///    转移：down[u] = val[u] + Σ max(down[v], 0)，其中 val[u] = 1 或 -1。
    /// 2. 第二次 DFS（换根）：计算 up[u] —— 在整棵树中，包含 u 的连通子图的最大得分。
    ///    对 u 的子节点 v：
    ///      去掉 v 子树后，u 剩余部分的最大得分 = up[u] - max(down[v], 0)。
    ///      记这部分为 rest，则 up[v] = down[v] + max(rest, 0)。
    ///    最终答案即为 up[u]。
    pub fn max_subgraph_score(n: i32, edges: Vec<Vec<i32>>, good: Vec<i32>) -> Vec<i32> {
        let n = n as usize;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for e in &edges {
            let (x, y) = (e[0] as usize, e[1] as usize);
            graph[x].push(y);
            graph[y].push(x);
        }

        // 迭代后序遍历，避免递归深度过大
        // 第一遍：生成遍历顺序（父节点先入栈，逆序即为后序）
        let mut order = Vec::with_capacity(n);
        let mut parent = vec![usize::MAX; n];
        let mut stack = vec![0usize];
        let mut visited = vec![false; n];
        visited[0] = true;

        while let Some(u) = stack.pop() {
            order.push(u);
            for &v in &graph[u] {
                if !visited[v] {
                    visited[v] = true;
                    parent[v] = u;
                    stack.push(v);
                }
            }
        }

        // down[u]：以 u 为根的子树中，包含 u 的连通子图最大得分
        let mut down = vec![0i32; n];
        for &u in order.iter().rev() {
            let val = if good[u] == 1 { 1 } else { -1 };
            let mut sum = val;
            for &v in &graph[u] {
                if v == parent[u] {
                    continue;
                }
                sum += down[v].max(0);
            }
            down[u] = sum;
        }

        // up[u]：整棵树中，包含 u 的连通子图最大得分
        let mut up = down.clone();
        for &u in order.iter() {
            for &v in &graph[u] {
                if v == parent[u] {
                    continue;
                }
                // 去掉 v 子树后，u 剩余部分的最大得分
                let rest = up[u] - down[v].max(0);
                up[v] = down[v] + rest.max(0);
            }
        }

        up
    }
}
```
