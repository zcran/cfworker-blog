---
title: "leetcode-树7"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 在树上执行操作以后得到的最大分数

有一棵 n 个节点的无向树，节点编号为 0 到 n - 1 ，根节点编号为 0 。给你一个长度为 n - 1 的二维整数数组 edges 表示这棵树，其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 有一条边。

同时给你一个长度为 n 下标从 0 开始的整数数组 values ，其中 values[i] 表示第 i 个节点的值。

一开始你的分数为 0。在一次操作中，你选择一个节点 i，将 values[i] 的值 加 到你的得分上，然后将 values[i] 设置 为 0。这三个步骤作为一个单一操作同时发生。

如果从根节点出发，到 任意 叶子节点经过的路径上的节点值之和都 不等于 0 ，那么我们称这棵树是 健康的 。

你可以对这棵树执行任意次操作，但要求执行完所有操作以后树是 健康的 ，请你返回你可以获得的 最大分数 。


```
impl Solution {
    /// 树形 DP
    ///
    /// 核心思路：
    /// 1. 最终得分 = 所有节点值之和 - 被“舍弃”的节点值之和。
    ///    因为每个节点最多被操作一次，操作后值归零，我们希望在保证健康的前提下，
    ///    尽可能少地舍弃节点值，即最大化得分。
    /// 2. 健康条件：从根到任意叶子节点的路径上，节点值之和 ≠ 0。
    ///    若某条路径上所有节点都被操作（值归零），则路径和为 0，不健康。
    /// 3. 对于每个节点 x，定义 loss(x) = 为使以 x 为根的子树健康，
    ///    至少需要“舍弃”的节点值之和（即被操作掉的节点值之和）。
    ///    - 若 x 是叶子：必须保留 values[x]，否则路径和为 0，
    ///      所以 loss(x) = values[x]（保留它，不能舍弃）。
    ///      但更准确地说：叶子节点若被舍弃则路径和为 0，所以叶子必须保留，
    ///      即“舍弃”的代价为 0？不，这里 loss 表示需要保留的最小值，
    ///      最终用总和减去保留的最小值。但原解法中 loss 表示“必须保留的最小值”，
    ///      总和减去它即为最大得分。
    /// 4. 对于非叶子 x：
    ///    - 可以选择舍弃 x（得分为 values[x]，但 x 变为 0），
    ///      此时每条子路径必须健康，故 loss = min(values[x], sum(loss(child)))？
    ///      原解法：loss(x) = min(values[x], sum(loss(child)))。
    ///      含义：要么保留 x（代价 values[x]），要么舍弃 x 但子节点各自保留 loss(child)。
    ///      取较小者作为“必须保留”的值。
    pub fn maximum_score_after_operations(edges: Vec<Vec<i32>>, values: Vec<i32>) -> i64 {
        let n = values.len();

        // 建邻接表，用 -1 标记根的特殊父节点，同时用于区分叶子
        let mut graph: Vec<Vec<i32>> = vec![Vec::new(); n];
        graph[0].push(-1); // 根节点加入 -1，用于区分叶子（叶子度数为 1，且只有父节点）
        for edge in &edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v as i32);
            graph[v].push(u as i32);
        }

        // 迭代后序遍历，避免递归深度过大
        // 第一遍：生成遍历顺序
        let mut order = Vec::with_capacity(n);
        let mut stack = vec![(0usize, usize::MAX)];
        let mut visited = vec![false; n];
        visited[0] = true;

        while let Some((u, parent)) = stack.pop() {
            order.push((u, parent));
            for &v in &graph[u] {
                if v >= 0 && !visited[v as usize] {
                    visited[v as usize] = true;
                    stack.push((v as usize, u));
                }
            }
        }

        // loss[x]：为使以 x 为根的子树健康，至少需要保留的节点值之和
        let mut loss = vec![0i64; n];

        // 逆序处理（自底向上）
        for &(u, parent) in order.iter().rev() {
            // 叶子节点：graph[u].len() == 1（仅有一个邻居）
            // 注意根节点 0 加入了 -1，若 n == 1，则根也是叶子
            if graph[u].len() == 1 {
                loss[u] = values[u] as i64;
            } else {
                let mut sum_loss = 0i64;
                for &v in &graph[u] {
                    if v >= 0 && v as usize != parent {
                        sum_loss += loss[v as usize];
                    }
                }
                loss[u] = (values[u] as i64).min(sum_loss);
            }
        }

        // 总分 = 所有节点值之和 - 必须保留的最小值
        let total: i64 = values.iter().map(|&x| x as i64).sum();
        total - loss[0]
    }
}
```
