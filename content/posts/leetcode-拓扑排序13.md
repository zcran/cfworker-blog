---
title: "leetcode-拓扑排序13"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 并行课程 III

给你一个整数 n ，表示有 n 节课，课程编号从 1 到 n 。同时给你一个二维整数数组 relations ，其中 relations[j] = [prevCoursej, nextCoursej] ，表示课程 prevCoursej 必须在课程 nextCoursej 之前 完成（先修课的关系）。同时给你一个下标从 0 开始的整数数组 time ，其中 time[i] 表示完成第 (i+1) 门课程需要花费的 月份 数。

请你根据以下规则算出完成所有课程所需要的 最少 月份数：

如果一门课的所有先修课都已经完成，你可以在 任意 时间开始这门课程。
你可以 同时 上 任意门课程 。
请你返回完成所有课程所需要的 最少 月份数。

注意：测试数据保证一定可以完成所有课程（也就是先修课的关系构成一个有向无环图）。

```
impl Solution {
    /// 返回完成所有课程所需的最少学期数（每个课程耗时由 `time` 给出，`relations` 表示先修关系）。
    /// 本质是求有向无环图（DAG）中的最长路径和。
    pub fn minimum_time(n: i32, relations: Vec<Vec<i32>>, time: Vec<i32>) -> i32 {
        let n = n as usize;
        // 构建反向图：rev_graph[v] 存储所有指向 v 的前置课程（即 v 依赖的课程）
        let mut rev_graph = vec![Vec::new(); n];
        // 标记每个节点是否有出边（即是否作为其他课程的前置条件出现）
        let mut has_outgoing = vec![false; n];

        for rel in relations {
            let (prev, next) = (rel[0] as usize - 1, rel[1] as usize - 1);
            rev_graph[next].push(prev);   // 反向边：从后续课程指向前置课程
            has_outgoing[prev] = true;    // prev 有出边，因为它依赖 next
        }

        // 记忆化数组：memo[u] 表示从课程 u 开始（包含 u）直到完成所有依赖所需的最长时间
        let mut memo = vec![i32::MIN; n];

        /// 深度优先搜索 + 记忆化，返回以 `u` 为起点的最长路径耗时
        fn dfs(u: usize, rev_graph: &[Vec<usize>], time: &[i32], memo: &mut [i32]) -> i32 {
            if memo[u] != i32::MIN {
                return memo[u];
            }
            // 计算所有前置课程的最长耗时，取最大值（若无前置则取 0）
            let max_prev = rev_graph[u]
                .iter()
                .map(|&v| dfs(v, rev_graph, time, memo))
                .max()
                .unwrap_or(0);
            let res = max_prev + time[u];
            memo[u] = res;
            res
        }

        // 所有没有后继的课程（即终点节点）作为搜索起点，取其中的最大值
        (0..n)
            .filter(|&i| !has_outgoing[i])   // 没有出边 → 没有课程依赖它 → 是终点
            .map(|i| dfs(i, &rev_graph, &time, &mut memo))
            .max()
            .unwrap()
    }
}
```
