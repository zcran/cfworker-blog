---
title: "leetcode-图42"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 针对图的路径存在性查询 II

给你一个整数 n，表示图中的节点数量，这些节点按从 0 到 n - 1 编号。

同时给你一个长度为 n 的整数数组 nums，以及一个整数 maxDiff。

如果满足 |nums[i] - nums[j]| <= maxDiff（即 nums[i] 和 nums[j] 的 绝对差 至多为 maxDiff），则节点 i 和节点 j 之间存在一条 无向边 。

此外，给你一个二维整数数组 queries。对于每个 queries[i] = [ui, vi]，找到节点 ui 和节点 vi 之间的 最短距离 。如果两节点之间不存在路径，则返回 -1。

返回一个数组 answer，其中 answer[i] 是第 i 个查询的结果。

注意：节点之间的边是无权重（unweighted）的。


```
impl Solution {
    pub fn path_existence_queries(n: i32, nums: Vec<i32>, max_diff: i32, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;

        // 按 nums 值排序，构建链式关系
        let mut order: Vec<usize> = (0..n).collect();
        order.sort_unstable_by_key(|&i| nums[i]);

        // 构建跳表：jump[j][i] 表示从位置 i 向上跳 2^j 步到达的位置
        let max_log = (n as f64).log2().ceil() as usize + 1;
        let mut jump = vec![vec![0; n]; max_log];

        let mut left = 0;
        for i in 0..n {
            let threshold = nums[order[i]] - max_diff;
            while nums[order[left]] < threshold {
                left += 1;
            }
            jump[0][i] = left;
        }

        // 倍增预处理
        for j in 1..max_log {
            for i in 0..n {
                jump[j][i] = jump[j - 1][jump[j - 1][i]];
            }
        }

        // 每个节点在排序后的位置
        let mut pos = vec![0; n];
        for (i, &node) in order.iter().enumerate() {
            pos[node] = i;
        }

        // 处理查询
        let mut ans = Vec::with_capacity(queries.len());
        for query in queries {
            let (mut u, mut v) = (pos[query[0] as usize], pos[query[1] as usize]);
            if u == v {
                ans.push(0);
                continue;
            }
            if u > v {
                std::mem::swap(&mut u, &mut v);
            }

            // 从 v 向上跳到最接近 u 的位置（但不能超过 u）
            let mut dist = 0;
            for j in (0..max_log).rev() {
                let ancestor = jump[j][v];
                if ancestor > u {
                    v = ancestor;
                    dist += 1 << j;
                }
            }

            // 检查是否能一步到达 u
            if jump[0][v] <= u {
                ans.push(dist + 1);
            } else {
                ans.push(-1);
            }
        }

        ans
    }
}
```
