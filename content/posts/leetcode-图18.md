---
title: "leetcode-图18"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 找出星型图的中心节点

有一个无向的 星型 图，由 n 个编号从 1 到 n 的节点组成。星型图有一个 中心 节点，并且恰有 n - 1 条边将中心节点与其他每个节点连接起来。

给你一个二维整数数组 edges ，其中 edges[i] = [ui, vi] 表示在节点 ui 和 vi 之间存在一条边。请你找出并返回 edges 所表示星型图的中心节点。


```
impl Solution {
    pub fn find_center(edges: Vec<Vec<i32>>) -> i32 {
        // 中心节点必然出现在每条边中，比较前两条边即可确定
        let (u1, v1) = (edges[0][0], edges[0][1]);
        let (u2, v2) = (edges[1][0], edges[1][1]);

        // 如果第一条边的某个端点出现在第二条边中，则该端点就是中心
        if u1 == u2 || u1 == v2 { u1 } else { v1 }
    }
}
```
