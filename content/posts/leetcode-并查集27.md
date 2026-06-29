---
title: "leetcode-并查集27"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 验证二叉树

二叉树上有 n 个节点，按从 0 到 n - 1 编号，其中节点 i 的两个子节点分别是 leftChild[i] 和 rightChild[i]。

只有 所有 节点能够形成且 只 形成 一颗 有效的二叉树时，返回 true；否则返回 false。

如果节点 i 没有左子节点，那么 leftChild[i] 就等于 -1。右子节点也符合该规则。

注意：节点没有值，本问题中仅仅使用节点编号。


```
impl Solution {
    /// 验证给定的左右子节点数组是否能构成唯一有效的二叉树。
    /// 条件：
    /// 1. 每个节点入度 ≤ 1（不能有多个父节点）
    /// 2. 恰好一个根节点（入度为 0）
    /// 3. 从根节点出发，所有节点都能访问到（无环，且连通）
    pub fn validate_binary_tree_nodes(n: i32, left_child: Vec<i32>, right_child: Vec<i32>) -> bool {
        let n = n as usize;
        let mut indegree = vec![0; n];
        let mut has_parent = vec![false; n]; // 优化：直接标记是否有父节点

        // 1. 统计入度，并检查每个节点入度是否 > 1
        for i in 0..n {
            let l = left_child[i];
            let r = right_child[i];
            if l != -1 {
                let child = l as usize;
                if has_parent[child] {
                    return false; // 已有父节点 → 入度 > 1
                }
                has_parent[child] = true;
                indegree[child] += 1;
            }
            if r != -1 {
                let child = r as usize;
                if has_parent[child] {
                    return false;
                }
                has_parent[child] = true;
                indegree[child] += 1;
            }
        }

        // 2. 找根节点：入度为 0 的节点，且只能有一个
        let mut root = None;
        for i in 0..n {
            if indegree[i] == 0 {
                if root.is_some() {
                    return false; // 多个根节点
                }
                root = Some(i);
            }
        }
        let root = match root {
            Some(r) => r,
            None => return false, // 没有根节点（环）
        };

        // 3. BFS/DFS 检查是否所有节点都能从根节点访问到
        let mut stack = vec![root];
        let mut visited = vec![false; n];
        visited[root] = true;
        let mut count = 1;

        while let Some(node) = stack.pop() {
            let l = left_child[node];
            let r = right_child[node];
            if l != -1 {
                let child = l as usize;
                if visited[child] {
                    return false; // 环
                }
                visited[child] = true;
                count += 1;
                stack.push(child);
            }
            if r != -1 {
                let child = r as usize;
                if visited[child] {
                    return false;
                }
                visited[child] = true;
                count += 1;
                stack.push(child);
            }
        }

        count == n // 所有节点都被访问到
    }
}
```
