---
title: "leetcode-图17"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 统计点对的数目

给你一个无向图，无向图由整数 n  ，表示图中节点的数目，和 edges 组成，其中 edges[i] = [ui, vi] 表示 ui 和 vi 之间有一条无向边。同时给你一个代表查询的整数数组 queries 。

第 j 个查询的答案是满足如下条件的点对 (a, b) 的数目：

a < b

cnt 是与 a 或者 b 相连的边的数目，且 cnt 严格大于 queries[j] 。

请你返回一个数组 answers ，其中 answers.length == queries.length 且 answers[j] 是第 j 个查询的答案。

请注意，图中可能会有 多重边 。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_pairs(n: i32, edges: Vec<Vec<i32>>, queries: Vec<i32>) -> Vec<i32> {
        let n = n as usize;

        // 计算每个节点的度
        let mut deg = vec![0; n];
        // 统计每条边出现的次数（多重边处理）
        let mut edge_count: HashMap<u64, i32> = HashMap::new();

        for e in edges {
            let (mut u, mut v) = (e[0] as usize - 1, e[1] as usize - 1);
            if u > v {
                std::mem::swap(&mut u, &mut v);
            }
            deg[u] += 1;
            deg[v] += 1;
            let key = (u as u64) << 32 | v as u64;
            *edge_count.entry(key).or_insert(0) += 1;
        }

        // 将度排序，用于双指针计算
        let mut sorted_deg = deg.clone();
        sorted_deg.sort_unstable();

        let mut ans = Vec::with_capacity(queries.len());

        for &q in &queries {
            // 双指针计算：满足 deg[a] + deg[b] > q 的点对数
            let mut count = 0;
            let mut left = 0;
            let mut right = n - 1;

            while left < right {
                if sorted_deg[left] + sorted_deg[right] > q {
                    // 左指针固定时，left 到 right 之间所有点都满足
                    count += right - left;
                    right -= 1;
                } else {
                    left += 1;
                }
            }

            // 修正：减去同一对点之间的边导致的重复计数
            for (&key, &multi) in &edge_count {
                let u = (key >> 32) as usize;
                let v = (key & 0xFFFFFFFF) as usize;
                let sum = deg[u] + deg[v];
                // 如果 sum > q 但 sum - multi <= q，说明被错误计入
                if sum > q && sum - multi <= q {
                    count -= 1;
                }
            }

            ans.push(count as i32);
        }

        ans
    }
}
```
