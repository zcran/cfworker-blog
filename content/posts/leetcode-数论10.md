---
title: "leetcode-数论10"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 互质树

给你一个 n 个节点的树（也就是一个无环连通无向图），节点编号从 0 到 n - 1 ，且恰好有 n - 1 条边，每个节点有一个值。树的 根节点 为 0 号点。

给你一个整数数组 nums 和一个二维数组 edges 来表示这棵树。nums[i] 表示第 i 个点的值，edges[j] = [uj, vj] 表示节点 uj 和节点 vj 在树中有一条边。

当 gcd(x, y) == 1 ，我们称两个数 x 和 y 是 互质的 ，其中 gcd(x, y) 是 x 和 y 的 最大公约数 。

从节点 i 到 根 最短路径上的点都是节点 i 的祖先节点。一个节点 不是 它自己的祖先节点。

请你返回一个大小为 n 的数组 ans ，其中 ans[i]是离节点 i 最近的祖先节点且满足 nums[i] 和 nums[ans[i]] 是 互质的 ，如果不存在这样的祖先节点，ans[i] 为 -1 。


```
use std::collections::VecDeque;

impl Solution {
    /// 为每个节点找到最近的祖先，使得 nums[ancestor] 与 nums[node] 互质
    ///
    /// 核心思路：
    /// 1. 值域只有 1..=50，预处理所有互质对
    /// 2. DFS 遍历树，维护当前路径上每个值最近出现的节点
    /// 3. 对当前节点，检查所有与 nums[node] 互质的值，找到深度最大的祖先
    pub fn get_coprimes(nums: Vec<i32>, edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = nums.len();

        // ---------- 1. 建图 ----------
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // ---------- 2. 预处理互质关系 ----------
        // 值域只有 1..=50，直接暴力枚举
        const MAX_VAL: usize = 50;
        let mut coprime_pairs = vec![Vec::new(); MAX_VAL + 1];

        for a in 1..=MAX_VAL {
            for b in 1..=MAX_VAL {
                if Self::gcd(a as i32, b as i32) == 1 {
                    coprime_pairs[a].push(b);
                }
            }
        }

        // ---------- 3. DFS ----------
        // path_stack[val] 存储当前 DFS 路径上值为 val 的节点列表（按深度递增）
        let mut path_stack = vec![Vec::new(); MAX_VAL + 1];
        let mut ans = vec![-1; n];
        let mut depth = vec![0; n];

        Self::dfs(
            0,           // 当前节点
            0,           // 父节点（避免回退）
            0,           // 当前深度
            &nums,
            &graph,
            &coprime_pairs,
            &mut path_stack,
            &mut ans,
            &mut depth,
        );

        ans
    }

    /// DFS 递归遍历树
    fn dfs(
        node: usize,
        parent: usize,
        cur_depth: i32,
        nums: &[i32],
        graph: &[Vec<usize>],
        coprime_pairs: &[Vec<usize>],
        path_stack: &mut [Vec<usize>],
        ans: &mut [i32],
        depth: &mut [i32],
    ) {
        depth[node] = cur_depth;

        // 检查与当前节点值互质的所有值
        let val = nums[node] as usize;
        let mut best_ancestor = -1;
        let mut best_depth = -1;

        for &coprime_val in &coprime_pairs[val] {
            if let Some(&ancestor) = path_stack[coprime_val].last() {
                // 找到深度最大的祖先
                if depth[ancestor] > best_depth {
                    best_depth = depth[ancestor];
                    best_ancestor = ancestor as i32;
                }
            }
        }
        ans[node] = best_ancestor;

        // 将当前节点加入路径栈
        path_stack[val].push(node);

        // 遍历子节点
        for &neighbor in &graph[node] {
            if neighbor != parent {
                Self::dfs(
                    neighbor,
                    node,
                    cur_depth + 1,
                    nums,
                    graph,
                    coprime_pairs,
                    path_stack,
                    ans,
                    depth,
                );
            }
        }

        // 回溯：从路径栈中移除当前节点
        path_stack[val].pop();
    }

    /// 计算最大公约数（欧几里得算法）
    #[inline]
    fn gcd(a: i32, b: i32) -> i32 {
        if b == 0 {
            a
        } else {
            Self::gcd(b, a % b)
        }
    }
}
```
