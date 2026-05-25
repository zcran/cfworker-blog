---
title: "leetcode-拓扑排序5"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 找到最终的安全状态


有一个有 n 个节点的有向图，节点按 0 到 n - 1 编号。图由一个 索引从 0 开始 的 2D 整数数组 graph表示， graph[i]是与节点 i 相邻的节点的整数数组，这意味着从节点 i 到 graph[i]中的每个节点都有一条边。

如果一个节点没有连出的有向边，则该节点是 终端节点 。如果从该节点开始的所有可能路径都通向 终端节点（或另一个安全节点），则该节点为 安全节点。

返回一个由图中所有 安全节点 组成的数组作为答案。答案数组中的元素应当按 升序 排列。

```
impl Solution {
    /// 找出最终安全的节点（LeetCode 802）。
    ///
    /// 使用三色 DFS 标记法：
    /// - Unvisited = 0 未访问
    /// - Visiting = 1 正在访问（在当前 DFS 栈中）
    /// - Safe     = 2 安全节点
    /// - Unsafe   = 3 不安全节点（已在环中或能到达环）
    ///
    /// 一个节点是安全当且仅当从它出发的所有路径都不进入环。
    pub fn eventual_safe_nodes(graph: Vec<Vec<i32>>) -> Vec<i32> {
        let n = graph.len();
        let mut state = vec![State::Unvisited; n];

        // 函数式筛选所有安全节点
        (0..n)
            .filter(|&i| Self::is_safe(&graph, &mut state, i))
            .map(|i| i as i32)
            .collect()
    }

    /// DFS 判断节点 `u` 是否安全
    fn is_safe(graph: &[Vec<i32>], state: &mut [State], u: usize) -> bool {
        use State::*;

        match state[u] {
            Safe => return true,
            Unsafe | Visiting => return false, // 访问中（有环）或不安全节点
            Unvisited => { /* 继续执行 DFS */ }
        }

        // 标记为正在访问
        state[u] = Visiting;

        // 所有后继必须安全
        let is_safe_node = graph[u]
            .iter()
            .all(|&v| Self::is_safe(graph, state, v as usize));

        // 回溯后更新状态
        state[u] = if is_safe_node { Safe } else { Unsafe };
        is_safe_node
    }
}

/// 节点在 DFS 中的状态
#[derive(Copy, Clone, PartialEq)]
enum State {
    Unvisited = 0,
    Visiting = 1,
    Safe = 2,
    Unsafe = 3,
}
```
