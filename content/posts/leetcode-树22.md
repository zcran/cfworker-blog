---
title: "leetcode-树22"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 树组的交互代价总和

给你一个整数 n 和一棵包含 n 个节点、编号从 0 到 n - 1 的无向树。树由一个长度为 n - 1 的二维数组 edges 表示，其中 edges[i] = [ui, vi] 表示节点 ui 和 vi 之间存在一条无向边。

同时给定一个长度为 n 的整数数组 group，其中 group[i] 表示分配给节点 i 的组标签。

如果 group[u] == group[v]，则认为节点 u 和 v 属于同一组。
交互代价 定义为节点 u 和 v 之间的唯一路径上的边数。

返回所有满足条件的 无序 节点对 (u, v) （其中 u != v 且 group[u] == group[v]）的交互代价之和。如果没有这样的节点对，返回 0。


```
impl Solution {
    /// 思路：
    /// 对于每个组标签 g，统计该组内所有节点对 (u, v) 的距离之和。
    /// 树上两点距离 = 路径边数。对所有同组节点对求和，等价于：
    /// 对每条边，计算它被多少对同组节点“跨过”，即 cnt * (tot - cnt)，
    /// 其中 cnt 是该边一侧（子树内）属于该组的节点数，tot 是该组总节点数。
    ///
    /// 做法：
    /// 1. 统计每个组的总节点数 total[g]。
    /// 2. 对每个非空组 g，以 0 为根做一次 DFS：
    ///    - 返回子树内属于组 g 的节点数 cnt_x；
    ///    - 对每个子节点 y，累加 cnt_y * (total[g] - cnt_y) 到答案。
    ///
    /// 优化：
    /// - 用迭代后序遍历代替递归，避免深度过大。
    /// - 对每个组单独遍历会重复 O(n * 组数)，这里采用一次遍历处理所有组：
    ///   在自底向上的过程中，对每个节点维护其子树内每个组的计数。
    ///   但这样内存为 O(n * 组数)，可能过大。
    ///   由于组数最多 n，最坏 O(n^2)。改用对每个非空组单独 DFS，总复杂度 O(n * 非空组数)，
    ///   最坏也是 O(n^2)，但常数较小，且题目通常组数有限。
    ///   这里采用官方思路：对每个组单独 DFS，简洁清晰。
    pub fn interaction_costs(n: i32, edges: Vec<Vec<i32>>, group: Vec<i32>) -> i64 {
        let n = n as usize;

        // 建邻接表
        let mut graph = vec![Vec::new(); n];
        for e in &edges {
            let (x, y) = (e[0] as usize, e[1] as usize);
            graph[x].push(y);
            graph[y].push(x);
        }

        // 统计每个组的总节点数
        let max_group = *group.iter().max().unwrap() as usize;
        let mut total = vec![0i32; max_group + 1];
        for &g in &group {
            total[g as usize] += 1;
        }

        // 预处理后序顺序（以 0 为根），避免每组重复建序
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

        let mut ans = 0i64;

        // 对每个非空组单独处理
        for target in 1..=max_group {
            let tot = total[target];
            if tot == 0 {
                continue;
            }

            // cnt[u]：以 u 为根的子树中属于 target 组的节点数
            let mut cnt = vec![0i32; n];

            // 逆序（自底向上）
            for &u in order.iter().rev() {
                let mut c = if group[u] as usize == target { 1 } else { 0 };
                for &v in &graph[u] {
                    if v == parent[u] {
                        continue;
                    }
                    // 边 (u, v) 被跨过的同组节点对数
                    ans += cnt[v] as i64 * (tot - cnt[v]) as i64;
                    c += cnt[v];
                }
                cnt[u] = c;
            }
        }

        ans
    }
}
```
