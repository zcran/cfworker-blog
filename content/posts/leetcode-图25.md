---
title: "leetcode-图25"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 树上最大得分和路径

一个 n 个节点的无向树，节点编号为 0 到 n - 1 ，树的根结点是 0 号节点。给你一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ai, bi] ，表示节点 ai 和 bi 在树中有一条边。

在每一个节点 i 处有一扇门。同时给你一个都是偶数的数组 amount ，其中 amount[i] 表示：

· 如果 amount[i] 的值是负数，那么它表示打开节点 i 处门扣除的分数。
· 如果 amount[i] 的值是正数，那么它表示打开节点 i 处门加上的分数。

游戏按照如下规则进行：

· 一开始，Alice 在节点 0 处，Bob 在节点 bob 处。
· 每一秒钟，Alice 和 Bob 分别 移动到相邻的节点。Alice 朝着某个 叶子结点 移动，Bob 朝着节点 0 移动。
· 对于他们之间路径上的 每一个 节点，Alice 和 Bob 要么打开门并扣分，要么打开门并加分。注意：
    · 如果门 已经打开 （被另一个人打开），不会有额外加分也不会扣分。
    · 如果 Alice 和 Bob 同时 到达一个节点，他们会共享这个节点的加分或者扣分。换言之，如果打开这扇门扣 c 分，那么 Alice 和 Bob 分别扣 c / 2 分。如果这扇门的加分为 c ，那么他们分别加 c / 2 分。
· 如果 Alice 到达了一个叶子结点，她会停止移动。类似的，如果 Bob 到达了节点 0 ，他也会停止移动。注意这些事件互相 独立 ，不会影响另一方移动。

请你返回 Alice 朝最优叶子结点移动的 最大 净得分。


```
impl Solution {
    pub fn most_profitable_path(edges: Vec<Vec<i32>>, bob: i32, amount: Vec<i32>) -> i32 {
        let n = amount.len();
        let bob = bob as usize;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // 第一次 DFS：记录父节点和深度
        let mut parent = vec![0; n];
        let mut depth_a = vec![0; n];
        Self::dfs_parent(&graph, 0, n, &mut parent, &mut depth_a);

        // 计算 Bob 到达每个节点的时间（B 数组）
        let mut time_b = vec![n + 1; n];
        let mut cur = bob;
        let mut step = 0;
        while cur != n {  // 父节点为 n 表示到达根节点的父节点（哨兵）
            time_b[cur] = step;
            cur = parent[cur];
            step += 1;
        }

        // 第二次 DFS：枚举 Alice 到达每个叶子节点的路径
        let mut ans = i32::MIN;
        Self::dfs_score(&graph, &amount, &parent, &depth_a, &time_b, 0, 0, &mut ans);
        ans
    }

    // DFS 计算父节点和深度
    fn dfs_parent(graph: &[Vec<usize>], node: usize, parent_node: usize, parent: &mut [usize], depth: &mut [i32]) {
        parent[node] = parent_node;
        for &next in &graph[node] {
            if next != parent_node {
                depth[next] = depth[node] + 1;
                Self::dfs_parent(graph, next, node, parent, depth);
            }
        }
    }

    // DFS 计算 Alice 到达叶子节点的得分
    fn dfs_score(
        graph: &[Vec<usize>],
        amount: &[i32],
        parent: &[usize],
        depth_a: &[i32],
        time_b: &[usize],
        node: usize,
        mut score: i32,
        ans: &mut i32,
    ) {
        // 计算当前节点的得分贡献
        let t_a = depth_a[node] as usize;
        if t_a < time_b[node] {
            // Alice 先到，获得全部分数
            score += amount[node];
        } else if t_a == time_b[node] {
            // 同时到达，平分分数
            score += amount[node] / 2;
        }
        // 如果 Alice 后到，分数已被 Bob 取走，不贡献

        // 检查是否为叶子节点（根节点只有一个邻居时也是叶子）
        let mut is_leaf = true;
        for &next in &graph[node] {
            if next != parent[node] {
                is_leaf = false;
                Self::dfs_score(graph, amount, parent, depth_a, time_b, next, score, ans);
            }
        }

        // 叶子节点：更新答案
        if is_leaf && node != 0 {  // 根节点不是叶子，除非 n == 1
            *ans = (*ans).max(score);
        } else if is_leaf && node == 0 && graph[0].is_empty() {
            // 只有一个节点的情况
            *ans = (*ans).max(score);
        }
    }
}
```
