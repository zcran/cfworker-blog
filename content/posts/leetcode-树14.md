---
title: "leetcode-树14"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 子树反转和

给你一棵以节点 0 为根节点包含 n 个节点的无向树，节点编号从 0 到 n - 1。该树由长度为 n - 1 的二维整数数组 edges 表示，其中 edges[i] = [ui, vi] 表示节点 ui 和 vi 之间有一条边。

同时给你一个整数 k 和长度为 n 的整数数组 nums，其中 nums[i] 表示节点 i 的值。

你可以对部分节点执行 反转操作 ，该操作需满足以下条件：

· 子树反转操作：

    · 当你反转一个节点时，以该节点为根的 子树 中所有节点的值都乘以 -1。

· 反转之间的距离限制：

    · 你只能在一个节点与其他已反转节点“足够远”的情况下反转它。

    · 具体而言，如果你反转两个节点 a 和 b，并且其中一个是另一个的祖先（即 LCA(a, b) = a 或 LCA(a, b) = b），那么它们之间的距离（它们之间路径上的边数）必须至少为 k。

返回应用 反转操作 后树上节点值的 最大 可能 总和 。

在一棵有根树中，某个节点 v 的子树是指所有路径到根节点包含 v 的节点集合。


```
use std::array::from_fn;
use std::collections::VecDeque;

impl Solution {
    pub fn subtree_inversion_sum(edges: Vec<Vec<i32>>, nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;

        // 建无向图
        let mut neighbors = vec![Vec::new(); n];
        for e in &edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            neighbors[u].push(v);
            neighbors[v].push(u);
        }

        let mut parents = vec![0usize; n];
        let mut children_of_node = vec![Vec::new(); n];
        let mut sum = vec![0i64; n];
        let mut k_descendants = vec![Vec::new(); n];
        let mut dp = vec![[0i64; 2]; n];

        // 迭代 DFS 建立父子关系、后序顺序，并计算 k 层内后代
        let mut leaves: VecDeque<usize> = VecDeque::new();
        let mut stack = vec![(0usize, true)];
        let mut path = Vec::new();

        while let Some((node, down)) = stack.pop() {
            if down {
                stack.push((node, false));
                let mut is_leaf = true;

                for &child in &neighbors[node] {
                    if Some(child) != path.last().copied() {
                        parents[child] = node;
                        children_of_node[node].push(child);
                        stack.push((child, true));
                        is_leaf = false;
                    }
                }

                path.push(node);

                if is_leaf {
                    leaves.push_back(node);
                    if path.len() > k {
                        let anc = path[path.len() - k - 1];
                        k_descendants[anc].push(node);
                        sum[anc] -= nums[node] as i64;
                    }
                }
            } else {
                path.pop();
            }
        }

        // 自底向上 DP
        let mut visited = vec![false; n];
        let mut out_degree: Vec<usize> = children_of_node.iter().map(|c| c.len()).collect();
        let mut queue = leaves;

        while let Some(node) = queue.pop_front() {
            let n = node;
            if visited[n] {
                continue;
            }
            visited[n] = true;

            dp[n] = from_fn(|j| {
                // 选择 1：不反转 n
                let sign1 = if j == 0 { 1 } else { -1 };
                let val1 = sign1 * nums[n] as i64
                    + children_of_node[n]
                        .iter()
                        .map(|&c| dp[c][j])
                        .sum::<i64>();

                // 选择 2：反转 n
                let sign2 = if j == 0 { -1 } else { 1 };
                let val2 = sign2 * (nums[n] as i64 + sum[n])
                    + k_descendants[n]
                        .iter()
                        .map(|&d| dp[d][1 - j])
                        .sum::<i64>();

                val1.max(val2)
            });

            if node == 0 {
                continue;
            }

            let parent = parents[n];
            let mut kd_of_parent: Vec<usize> = k_descendants[n]
                .iter()
                .map(|&d| parents[d])
                .collect::<std::collections::HashSet<_>>()
                .into_iter()
                .collect();

            sum[parent] += sum[n] + nums[n] as i64
                - kd_of_parent
                    .iter()
                    .map(|&d| nums[d] as i64)
                    .sum::<i64>();

            k_descendants[parent].append(&mut kd_of_parent);

            out_degree[parent] -= 1;
            if out_degree[parent] == 0 {
                queue.push_back(parent); // parent 已是 usize，无需转换
            }
        }

        dp[0][0]
    }
}
```
