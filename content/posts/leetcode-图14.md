---
title: "leetcode-图14"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 可以到达所有点的最少点数目

给你一个 有向无环图 ， n 个节点编号为 0 到 n-1 ，以及一个边数组 edges ，其中 edges[i] = [fromi, toi] 表示一条从点  fromi 到点 toi 的有向边。

找到最小的点集使得从这些点出发能到达图中所有点。题目保证解存在且唯一。

你可以以任意顺序返回这些节点编号。


```
impl Solution {
    pub fn find_smallest_set_of_vertices(n: i32, edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;

        // 标记所有有入度的节点（即能被到达的节点）
        let mut has_incoming = vec![false; n];
        for e in edges {
            has_incoming[e[1] as usize] = true;
        }

        // 没有入度的节点必须作为起点
        has_incoming
            .into_iter()
            .enumerate()
            .filter_map(|(i, has)| if !has { Some(i as i32) } else { None })
            .collect()
    }
}
```
