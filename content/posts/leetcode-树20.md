---
title: "leetcode-树20"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 使叶子路径成本相等的最小增量

给你一个整数 n，以及一个无向树，该树以节点 0 为根节点，包含 n 个节点，节点编号从 0 到 n - 1。这棵树由一个长度为 n - 1 的二维数组 edges 表示，其中 edges[i] = [ui, vi] 表示节点 ui 和节点 vi 之间存在一条边。

每个节点 i 都有一个关联的成本 cost[i]，表示经过该节点的成本。

路径得分 定义为路径上所有节点成本的总和。

你的目标是通过给任意数量的节点 增加 成本（可以增加任意非负值），使得所有从根节点到叶子节点的路径得分 相等 。

返回需要增加成本的节点数的 最小值 。


```
impl Solution {
    /// 树形 DFS + 贪心
    ///
    /// 核心思路：
    /// 从根到每个叶子的路径得分需要相等。对于任意节点 x，其所有子分支对应的
    /// “从根到叶子”的路径得分中，只有最大的那些不需要额外增加成本，
    /// 其余较小的路径必须通过增加路径上某些节点的成本来补齐到最大值。
    ///
    /// 贪心策略：
    /// 在节点 x 处，统计其所有子分支返回的最大路径得分 max_s 以及达到 max_s 的分支数 cnt。
    /// 其余 (子分支数 - cnt) 个分支都需要至少增加一次成本。
    /// 答案累加 (子分支数 - cnt)。
    ///
    /// DFS 返回值：以 x 为根的子树中，从根到该子树内任意叶子的最大路径得分（含 x 的成本）。
    pub fn min_increase(n: i32, edges: Vec<Vec<i32>>, cost: Vec<i32>) -> i32 {
        let n = n as usize;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for e in &edges {
            let (x, y) = (e[0] as usize, e[1] as usize);
            graph[x].push(y);
            graph[y].push(x);
        }

        // 迭代后序遍历，避免递归深度过大
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

        // max_path[u]：从根到以 u 为根的子树中任意叶子的最大路径得分
        let mut max_path = vec![0i64; n];
        let mut ans = 0i32;

        // 逆序处理（自底向上）
        for &u in order.iter().rev() {
            // 叶子判定：除根外，度数为 1 的节点是叶子
            // 根节点若度数为 0（n == 1）也视为叶子
            let is_leaf = if u == 0 {
                graph[u].is_empty()
            } else {
                graph[u].len() == 1
            };

            if is_leaf {
                max_path[u] = cost[u] as i64;
                continue;
            }

            // 非叶子：统计子分支的最大路径得分
            let mut max_s = 0i64;
            let mut cnt = 0i32;
            let mut child_count = 0i32;

            for &v in &graph[u] {
                if v == parent[u] {
                    continue;
                }
                child_count += 1;
                // 子分支路径得分 = cost[u] + max_path[v]
                let mx = cost[u] as i64 + max_path[v];
                if mx > max_s {
                    max_s = mx;
                    cnt = 1;
                } else if mx == max_s {
                    cnt += 1;
                }
            }

            // 其余分支需要增加成本
            ans += child_count - cnt;
            max_path[u] = max_s;
        }

        ans
    }
}
```
