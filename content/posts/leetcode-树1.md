---
title: "leetcode-树1"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 从树中删除边的最小分数

存在一棵无向连通树，树中有编号从 0 到 n - 1 的 n 个节点， 以及 n - 1 条边。

给你一个下标从 0 开始的整数数组 nums ，长度为 n ，其中 nums[i] 表示第 i 个节点的值。另给你一个二维整数数组 edges ，长度为 n - 1 ，其中 edges[i] = [ai, bi] 表示树中存在一条位于节点 ai 和 bi 之间的边。

删除树中两条 不同 的边以形成三个连通组件。对于一种删除边方案，定义如下步骤以计算其分数：

1. 分别获取三个组件 每个 组件中所有节点值的异或值。
2. 最大 异或值和 最小 异或值的 差值 就是这一种删除边方案的分数。
  · 例如，三个组件的节点值分别是：[4,5,7]、[1,9] 和 [3,3,3] 。三个异或值分别是 4 ^ 5 ^ 7 = 6、1 ^ 9 = 8 和 3 ^ 3 ^ 3 = 3 。最大异或值是 8 ，最小异或值是 3 ，分数是 8 - 3 = 5 。

返回在给定树上执行任意删除边方案可能的 最小 分数。


```
use std::cmp::{max, min};

impl Solution {
    #[inline]
    fn calc(a: i32, b: i32, c: i32) -> i32 {
        max(a, max(b, c)) - min(a, min(b, c))
    }

    pub fn minimum_score(nums: Vec<i32>, edges: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // 整棵树的异或和
        let total_xor = nums.iter().fold(0, |acc, &x| acc ^ x);

        let mut ans = i32::MAX;

        // 第二层 DFS：在 ancestor 的子树中枚举第二条被删除的边
        // other_xor: 第一条边外侧部分的异或和
        // ancestor: 第一条边中当前节点的祖先（用于避免删除同一条边）
        fn dfs2(
            x: usize,
            parent: usize,
            other_xor: i32,
            ancestor: usize,
            nums: &[i32],
            graph: &[Vec<usize>],
            ans: &mut i32,
            total_xor: i32,
        ) -> i32 {
            let mut subtree_xor = nums[x];

            for &y in &graph[x] {
                if y == parent {
                    continue;
                }
                subtree_xor ^= dfs2(y, x, other_xor, ancestor, nums, graph, ans, total_xor);
            }

            // 不能删除与第一条边重合的边
            if parent == ancestor {
                return subtree_xor;
            }

            // 三个连通块的异或值：
            // 1. other_xor：第一条边外侧
            // 2. subtree_xor：第二条边隔出的子树
            // 3. total_xor ^ other_xor ^ subtree_xor：剩余中间部分
            *ans = min(
                *ans,
                Solution::calc(other_xor, subtree_xor, total_xor ^ other_xor ^ subtree_xor),
            );

            subtree_xor
        }

        // 第一层 DFS：枚举第一条被删除的边 (x, parent)
        fn dfs(
            x: usize,
            parent: usize,
            nums: &[i32],
            graph: &[Vec<usize>],
            ans: &mut i32,
            total_xor: i32,
        ) -> i32 {
            let mut subtree_xor = nums[x];

            for &y in &graph[x] {
                if y == parent {
                    continue;
                }
                subtree_xor ^= dfs(y, x, nums, graph, ans, total_xor);
            }

            // 以 (x, parent) 为第一条边，从 parent 开始枚举第二条边
            for &y in &graph[x] {
                if y == parent {
                    dfs2(y, x, subtree_xor, x, nums, graph, ans, total_xor);
                }
            }

            subtree_xor
        }

        // 从 0 号节点开始，虚拟父节点用 usize::MAX
        dfs(0, usize::MAX, &nums, &graph, &mut ans, total_xor);

        ans
    }
}
```
