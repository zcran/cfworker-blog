---
title: "leetcode-拓扑排序8"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 课程表 IV

你总共需要上 numCourses 门课，课程编号依次为 0 到 numCourses-1 。你会得到一个数组 prerequisite ，其中 prerequisites[i] = [ai, bi] 表示如果你想选 bi 课程，你 必须 先选 ai 课程。

有的课会有直接的先修课程，比如如果想上课程 1 ，你必须先上课程 0 ，那么会以 [0,1] 数对的形式给出先修课程数对。
先决条件也可以是 间接 的。如果课程 a 是课程 b 的先决条件，课程 b 是课程 c 的先决条件，那么课程 a 就是课程 c 的先决条件。

你也得到一个数组 queries ，其中 queries[j] = [uj, vj]。对于第 j 个查询，您应该回答课程 uj 是否是课程 vj 的先决条件。

返回一个布尔数组 answer ，其中 answer[j] 是第 j 个查询的答案。

```
use std::collections::VecDeque; // 虽未使用，但保留以表明可用

impl Solution {
    /// 检查每个查询 (u, v) 中 u 是否为 v 的先决条件（LeetCode 1462）。
    ///
    /// 算法：DFS + 记忆化搜索。
    /// 1. 建图：邻接表 `graph[u]` 存储 u 的直接后继课程。
    /// 2. 记忆化矩阵 `memo[u][v]` 缓存 u 是否能到达 v。
    /// 3. 对每个查询，通过 DFS 递归判断可达性，并填充缓存。
    pub fn check_if_prerequisite(
        num_courses: i32,
        prerequisites: Vec<Vec<i32>>,
        queries: Vec<Vec<i32>>,
    ) -> Vec<bool> {
        let n = num_courses as usize;
        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in prerequisites {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
        }

        // 记忆化矩阵：None 表示未计算，Some(bool) 表示 u 能否到达 v
        let mut memo = vec![vec![None; n]; n];

        // 函数式处理查询：迭代每个查询并收集结果
        queries
            .iter()
            .map(|q| {
                let u = q[0] as usize;
                let v = q[1] as usize;
                Self::dfs(u, v, &graph, &mut memo)
            })
            .collect()
    }

    /// 深度优先搜索判断从 `u` 是否能到达 `v`。
    /// * `graph` - 邻接表（只读）
    /// * `memo` - 可变引用，存储已计算结果
    fn dfs(
        u: usize,
        v: usize,
        graph: &[Vec<usize>],
        memo: &mut Vec<Vec<Option<bool>>>,
    ) -> bool {
        // 命中缓存则直接返回
        if let Some(res) = memo[u][v] {
            return res;
        }

        let mut reachable = false;
        for &next in &graph[u] {
            if next == v {
                reachable = true;
                break;
            }
            // 递归检查 next 是否能到达 v
            if Self::dfs(next, v, graph, memo) {
                reachable = true;
                break;
            }
        }

        // 更新缓存
        memo[u][v] = Some(reachable);
        reachable
    }
}
```
