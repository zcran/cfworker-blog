---
title: "leetcode-图24"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 边积分最高的节点

给你一个有向图，图中有 n 个节点，节点编号从 0 到 n - 1 ，其中每个节点都 恰有一条 出边。

图由一个下标从 0 开始、长度为 n 的整数数组 edges 表示，其中 edges[i] 表示存在一条从节点 i 到节点 edges[i] 的 有向 边。

节点 i 的 边积分 定义为：所有存在一条指向节点 i 的边的节点的 编号 总和。

返回 边积分 最高的节点。如果多个节点的 边积分 相同，返回编号 最小 的那个。


```
impl Solution {
    pub fn edge_score(edges: Vec<i32>) -> i32 {
        let n = edges.len();

        // 累积每个节点的边积分（使用 i64 防止溢出）
        let mut scores = vec![0i64; n];

        // 遍历每个节点 i，将其编号加到目标节点 edges[i] 的积分上
        for (i, &target) in edges.iter().enumerate() {
            scores[target as usize] += i as i64;
        }

        // 找到积分最高的节点（积分相同取编号最小）
        let mut max_score = -1i64;
        let mut ans = -1i32;

        for (i, &score) in scores.iter().enumerate() {
            if score > max_score {
                max_score = score;
                ans = i as i32;
            }
        }

        ans
    }
}
```
