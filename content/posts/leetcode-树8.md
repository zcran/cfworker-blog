---
title: "leetcode-树8"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 在带权树网络中统计可连接服务器对数目

给你一棵无根带权树，树中总共有 n 个节点，分别表示 n 个服务器，服务器从 0 到 n - 1 编号。同时给你一个数组 edges ，其中 edges[i] = [ai, bi, weighti] 表示节点 ai 和 bi 之间有一条双向边，边的权值为 weighti 。再给你一个整数 signalSpeed 。

如果两台服务器 a 和 b 是通过服务器 c 可连接的，则：

a < b ，a != c 且 b != c 。
从 c 到 a 的距离是可以被 signalSpeed 整除的。
从 c 到 b 的距离是可以被 signalSpeed 整除的。
从 c 到 b 的路径与从 c 到 a 的路径没有任何公共边。

请你返回一个长度为 n 的整数数组 count ，其中 count[i] 表示通过服务器 i 可连接 的服务器对的 数目 。


```
impl Solution {
    /// 枚举中心服务器 + DFS
    ///
    /// 核心思路：
    /// 对每个中心服务器 c，统计所有满足以下条件的服务器对 (a, b)：
    /// 1. a、b 位于 c 的不同子树分支（路径无公共边）。
    /// 2. 从 c 到 a、从 c 到 b 的距离都能被 signalSpeed 整除。
    ///
    /// 做法：
    /// - 对 c 的每个邻接分支，DFS 统计该分支内满足距离条件的节点数。
    /// - 用前缀乘积累加：每加入一个新分支的计数 cnt，与之前所有分支的计数之和 pre 相乘，
    ///   即可得到跨分支的有效对数，避免重复。
    pub fn count_pairs_of_connectable_servers(
        edges: Vec<Vec<i32>>,
        signal_speed: i32,
    ) -> Vec<i32> {
        let n = edges.len() + 1;

        // 建邻接表：(邻居, 边权)
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (x, y, w) = (edge[0] as usize, edge[1] as usize, edge[2]);
            graph[x].push((y, w));
            graph[y].push((x, w));
        }

        // 从 p 出发，沿分支向下 DFS，统计满足距离模 signal_speed == 0 的节点数
        // curr 为从中心 c 到当前节点 p 的累计距离模 signal_speed 的余数
        fn dfs(
            graph: &[Vec<(usize, i32)>],
            p: usize,
            parent: usize,
            curr: i32,
            signal_speed: i32,
        ) -> i32 {
            // 当前节点满足距离条件
            let mut count = if curr == 0 { 1 } else { 0 };

            for &(v, w) in &graph[p] {
                if v != parent {
                    count += dfs(graph, v, p, (curr + w) % signal_speed, signal_speed);
                }
            }
            count
        }

        let mut ans = vec![0i32; n];

        // 枚举每个节点作为中心服务器 c
        for c in 0..n {
            let mut pre = 0i32; // 之前所有分支中满足条件的节点数之和

            for &(v, w) in &graph[c] {
                // 从 c 出发，进入分支 v，初始余数为 w % signal_speed
                let cnt = dfs(&graph, v, c, w % signal_speed, signal_speed);
                // 当前分支与之前所有分支组合成有效节点对
                ans[c] += pre * cnt;
                pre += cnt;
            }
        }

        ans
    }
}
```
