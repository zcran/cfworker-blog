---
title: "leetcode-图30"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 最小化旅行的价格总和

现有一棵无向、无根的树，树中有 n 个节点，按从 0 到 n - 1 编号。给你一个整数 n 和一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条边。

每个节点都关联一个价格。给你一个整数数组 price ，其中 price[i] 是第 i 个节点的价格。

给定路径的 价格总和 是该路径上所有节点的价格之和。

另给你一个二维整数数组 trips ，其中 trips[i] = [starti, endi] 表示您从节点 starti 开始第 i 次旅行，并通过任何你喜欢的路径前往节点 endi 。

在执行第一次旅行之前，你可以选择一些 非相邻节点 并将价格减半。

返回执行所有旅行的最小价格总和。


```
impl Solution {
    pub fn minimum_total_price(n: i32, edges: Vec<Vec<i32>>, price: Vec<i32>, trips: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (u, v) = (edge[0] as usize, edge[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // 统计每个节点在 trips 中被访问的次数
        let mut count = vec![0; n];
        for trip in trips {
            let (start, end) = (trip[0] as usize, trip[1] as usize);
            Self::mark_path(start, n, &mut count, &graph, end);
        }

        // 树形 DP：返回 (不半价, 半价) 的最小总价
        let (not_half, half) = Self::dfs(0, 0, &price, &count, &graph);
        not_half.min(half)
    }

    /// 标记从 start 到 end 路径上的所有节点
    fn mark_path(node: usize, parent: usize, count: &mut [i32], graph: &[Vec<usize>], end: usize) -> bool {
        if node == end {
            count[node] += 1;
            return true;
        }

        for &next in &graph[node] {
            if next != parent && Self::mark_path(next, node, count, graph, end) {
                count[node] += 1;
                return true;
            }
        }
        false
    }

    /// 树形 DP：返回 (当前节点不半价, 当前节点半价) 的最小总价
    fn dfs(node: usize, parent: usize, price: &[i32], count: &[i32], graph: &[Vec<usize>]) -> (i32, i32) {
        let base = price[node] * count[node];
        let mut not_half = base;      // 当前节点不半价
        let mut half = base / 2;     // 当前节点半价

        for &next in &graph[node] {
            if next != parent {
                let (child_not_half, child_half) = Self::dfs(next, node, price, count, graph);
                // 当前节点不半价：子节点可以半价或不半价，取较小值
                not_half += child_not_half.min(child_half);
                // 当前节点半价：子节点不能半价（非相邻节点限制）
                half += child_not_half;
            }
        }

        (not_half, half)
    }
}
```
