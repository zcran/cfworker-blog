---
title: "leetcode-图21"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 合法重新排列数对

给你一个下标从 0 开始的二维整数数组 pairs ，其中 pairs[i] = [starti, endi] 。如果 pairs 的一个重新排列，满足对每一个下标 i （ 1 <= i < pairs.length ）都有 endi-1 == starti ，那么我们就认为这个重新排列是 pairs 的一个 合法重新排列 。

请你返回 任意一个 pairs 的合法重新排列。

注意：数据保证至少存在一个 pairs 的合法重新排列。


```
use std::collections::HashMap;

impl Solution {
    pub fn valid_arrangement(pairs: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        // 构建邻接表：每个节点的出边列表
        let mut graph: HashMap<i32, Vec<i32>> = HashMap::new();
        // 统计入度和出度
        let mut in_deg: HashMap<i32, i32> = HashMap::new();
        let mut out_deg: HashMap<i32, i32> = HashMap::new();

        for p in &pairs {
            let (u, v) = (p[0], p[1]);
            graph.entry(u).or_default().push(v);
            *out_deg.entry(u).or_insert(0) += 1;
            *in_deg.entry(v).or_insert(0) += 1;
        }

        // 寻找欧拉通路的起点：
        // 如果有节点出度比入度大 1，则从该节点开始
        // 否则从任意节点开始（所有节点入度 = 出度）
        let start = out_deg
            .iter()
            .find(|(node, out)| **out == in_deg.get(node).copied().unwrap_or(0) + 1)
            .map(|(node, _)| *node)
            .unwrap_or(pairs[0][0]);

        let mut ans = Vec::new();

        // Hierholzer 算法：递归构建欧拉通路
        fn dfs(u: i32, graph: &mut HashMap<i32, Vec<i32>>, ans: &mut Vec<Vec<i32>>) {
            while let Some(v) = graph.get_mut(&u).and_then(|edges| edges.pop()) {
                dfs(v, graph, ans);
                ans.push(vec![u, v]);
            }
        }

        dfs(start, &mut graph, &mut ans);
        ans.reverse();
        ans
    }
}
```
