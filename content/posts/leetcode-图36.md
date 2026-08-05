---
title: "leetcode-图36"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 标记所有节点需要的时间

给你一棵 无向 树，树中节点从 0 到 n - 1 编号。同时给你一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ui, vi] 表示节点 ui 和 vi 在树中有一条边。

一开始，所有 节点都 未标记 。对于节点 i ：

· 当 i 是奇数时，如果时刻 x - 1 该节点有 至少 一个相邻节点已经被标记了，那么节点 i 会在时刻 x 被标记。
· 当 i 是偶数时，如果时刻 x - 2 该节点有 至少 一个相邻节点已经被标记了，那么节点 i 会在时刻 x 被标记。

请你返回一个数组 times ，表示如果你在时刻 t = 0 标记节点 i ，那么时刻 times[i] 时，树中所有节点都会被标记。

请注意，每个 times[i] 的答案都是独立的，即当你标记节点 i 时，所有其他节点都未标记。


```
impl Solution {
    pub fn time_taken(edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = edges.len() + 1;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // sub_info[node] = (最长深度, 次长深度, 最长深度对应的子节点)
        let mut sub_info = vec![(0, 0, 0); n];

        // 第一次 DFS：计算以 0 为根时，每个节点的子树信息
        Self::dfs(0, 0, &graph, &mut sub_info);

        // ans[node] 表示以 node 为根时，整棵树的最大深度
        let mut ans = vec![0; n];

        // 第二次 DFS：换根 DP，计算每个节点作为根时的答案
        Self::reroot(0, 0, 0, &graph, &sub_info, &mut ans);

        ans
    }

    /// 计算子树信息：(最长深度, 次长深度, 最长深度对应的子节点)
    /// 边权规则：从 x 到 y 的边权 = 2 - y % 2（即如果 y 是奇数则权值为 1，否则为 2）
    fn dfs(node: usize, parent: usize, graph: &[Vec<usize>], sub_info: &mut [(i32, i32, usize)]) {
        let mut max_len = 0;
        let mut second_len = 0;
        let mut best_child = 0;

        for &next in &graph[node] {
            if next == parent {
                continue;
            }
            Self::dfs(next, node, graph, sub_info);

            // 从 node 到 next 的边权取决于 next 的奇偶性
            let weight = 2 - (next % 2) as i32;
            let depth = sub_info[next].0 + weight;

            if depth > max_len {
                second_len = max_len;
                max_len = depth;
                best_child = next;
            } else if depth > second_len {
                second_len = depth;
            }
        }

        sub_info[node] = (max_len, second_len, best_child);
    }

    /// 换根 DP：计算每个节点作为根时的答案
    /// from_up 表示从父节点方向能获得的最大深度（从当前节点出发往上走）
    fn reroot(
        node: usize,
        parent: usize,
        from_up: i32,
        graph: &[Vec<usize>],
        sub_info: &[(i32, i32, usize)],
        ans: &mut [i32],
    ) {
        let (max_len, second_len, best_child) = sub_info[node];
        ans[node] = max_len.max(from_up);

        for &next in &graph[node] {
            if next == parent {
                continue;
            }

            // 不经过 next 子树的备选最长路径：
            // 要么来自父节点方向 (from_up)，要么来自其他子节点
            let alternative = if next == best_child { second_len } else { max_len };
            let new_from_up = from_up.max(alternative);

            // 从 next 到 node 的边权取决于 node 的奇偶性
            let weight = 2 - (node % 2) as i32;
            Self::reroot(next, node, new_from_up + weight, graph, sub_info, ans);
        }
    }
}
```
