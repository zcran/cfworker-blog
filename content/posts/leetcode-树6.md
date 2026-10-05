---
title: "leetcode-树6"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 可以被 K 整除连通块的最大数目

给你一棵 n 个节点的无向树，节点编号为 0 到 n - 1 。给你整数 n 和一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 有一条边。

同时给你一个下标从 0 开始长度为 n 的整数数组 values ，其中 values[i] 是第 i 个节点的 值 。再给你一个整数 k 。

你可以从树中删除一些边，也可以一条边也不删，得到若干连通块。一个 连通块的值 定义为连通块中所有节点值之和。如果所有连通块的值都可以被 k 整除，那么我们说这是一个 合法分割 。

请你返回所有合法分割中，连通块数目的最大值 。


```
impl Solution {
    /// 贪心 + DFS
    ///
    /// 核心观察：
    /// 1. 整棵树的总和必须能被 k 整除，否则无法分割成所有连通块都被 k 整除。
    ///    但由于题目保证合法分割存在性由答案自然处理，这里只需按贪心统计。
    /// 2. 自底向上 DFS：若以某节点为根的子树和能被 k 整除，
    ///    则可以把该子树与父节点之间的边删除，形成一个合法连通块。
    /// 3. 贪心删除不会影响其他连通块的合法性，且能得到最多连通块数。
    pub fn max_k_divisible_components(
        n: i32,
        edges: Vec<Vec<i32>>,
        values: Vec<i32>,
        k: i32,
    ) -> i32 {
        let n = n as usize;
        let k = k as i64;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // 迭代 DFS，避免递归深度过大
        // 栈元素：(节点, 父节点, 是否已处理完子节点)
        // 使用后序遍历：先处理子节点，再根据子树和判断是否切割
        let mut ans = 0i32;

        // 用显式栈做后序遍历
        // 第一遍：记录遍历顺序
        let mut order = Vec::with_capacity(n);
        let mut stack = vec![(0usize, usize::MAX)];
        let mut visited = vec![false; n];
        visited[0] = true;

        while let Some((u, parent)) = stack.pop() {
            order.push((u, parent));
            for &v in &graph[u] {
                if !visited[v] {
                    visited[v] = true;
                    stack.push((v, u));
                }
            }
        }

        // 自底向上累加子树和
        let mut subtree_sum = vec![0i64; n];
        for i in 0..n {
            subtree_sum[i] = values[i] as i64;
        }

        // 逆序处理（后序遍历的逆序即为自底向上）
        for &(u, parent) in order.iter().rev() {
            if subtree_sum[u] % k == 0 {
                ans += 1;
            }
            if parent != usize::MAX {
                subtree_sum[parent] += subtree_sum[u];
            }
        }

        ans
    }
}
```
