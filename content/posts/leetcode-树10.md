---
title: "leetcode-树10"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 统计好节点的数目

现有一棵 无向 树，树中包含 n 个节点，按从 0 到 n - 1 标记。树的根节点是节点 0 。给你一个长度为 n - 1 的二维整数数组 edges，其中 edges[i] = [ai, bi] 表示树中节点 ai 与节点 bi 之间存在一条边。

如果一个节点的所有子节点为根的 子树 包含的节点数相同，则认为该节点是一个 好节点。

返回给定树中 好节点 的数量。

子树 指的是一个节点以及它所有后代节点构成的一棵树。


```
impl Solution {
    /// DFS 后序遍历
    ///
    /// 核心思路：
    /// 对每个节点，递归计算其子树的节点数，同时判断该节点的所有子节点为根的子树大小是否相同。
    /// 若相同（或没有子节点），则该节点是好节点。
    pub fn count_good_nodes(edges: Vec<Vec<i32>>) -> i32 {
        let n = edges.len() + 1;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (x, y) = (edge[0] as usize, edge[1] as usize);
            graph[x].push(y);
            graph[y].push(x);
        }

        // 迭代后序遍历，避免递归深度过大
        // 第一遍：生成遍历顺序（父节点先于子节点入栈，逆序即为后序）
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

        // subtree_size[i]：以 i 为根的子树节点数
        let mut subtree_size = vec![1usize; n];
        let mut ans = 0i32;

        // 逆序处理（自底向上）
        for &(u, parent) in order.iter().rev() {
            let mut valid = true;
            let mut first_child_size = 0usize;

            // 遍历子节点，检查子树大小是否一致
            for &v in &graph[u] {
                if v == parent {
                    continue;
                }
                let size = subtree_size[v];
                if first_child_size == 0 {
                    first_child_size = size;
                } else if size != first_child_size {
                    valid = false;
                }
                subtree_size[u] += size;
            }

            if valid {
                ans += 1;
            }
        }

        ans
    }
}
```
