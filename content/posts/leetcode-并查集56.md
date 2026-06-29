---
title: "leetcode-并查集56"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 针对图的路径存在性查询 I

给你一个整数 n，表示图中的节点数量，这些节点按从 0 到 n - 1 编号。

同时给你一个长度为 n 的整数数组 nums，该数组按 非递减 顺序排序，以及一个整数 maxDiff。

如果满足 |nums[i] - nums[j]| <= maxDiff（即 nums[i] 和 nums[j] 的 绝对差 至多为 maxDiff），则节点 i 和节点 j 之间存在一条 无向边 。

此外，给你一个二维整数数组 queries。对于每个 queries[i] = [ui, vi]，需要判断节点 ui 和 vi 之间是否存在路径。

返回一个布尔数组 answer，其中 answer[i] 等于 true 表示在第 i 个查询中节点 ui 和 vi 之间存在路径，否则为 false。


```
impl Solution {
    pub fn path_existence_queries(
        n: i32,
        nums: Vec<i32>,
        max_diff: i32,
        queries: Vec<Vec<i32>>,
    ) -> Vec<bool> {
        let n = n as usize;

        // 构建连通分量标记：如果相邻节点值差 <= max_diff，则它们在同一分量中
        let mut comp = vec![0; n];
        let mut comp_id = 0;

        for i in 0..n {
            if i > 0 && nums[i] - nums[i - 1] <= max_diff {
                comp[i] = comp_id; // 与前一节点在同一分量
            } else {
                comp_id += 1;
                comp[i] = comp_id; // 新分量
            }
        }

        // 处理查询
        queries
            .iter()
            .map(|q| comp[q[0] as usize] == comp[q[1] as usize])
            .collect()
    }
}
```
