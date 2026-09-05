---
title: "leetcode-堆33"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 包含每个查询的最小区间

给你一个二维整数数组 intervals ，其中 intervals[i] = [leftˇi, rightˇi] 表示第 i 个区间开始于 leftˇi 、结束于 rightˇi（包含两侧取值，闭区间）。区间的 长度 定义为区间中包含的整数数目，更正式地表达是 rightˇi - leftˇi + 1 。

再给你一个整数数组 queries 。第 j 个查询的答案是满足 leftˇi <= queries[j] <= rightˇi 的 长度最小区间 i 的长度 。如果不存在这样的区间，那么答案是 -1 。

以数组形式返回对应查询的所有答案。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn min_interval(mut intervals: Vec<Vec<i32>>, queries: Vec<i32>) -> Vec<i32> {
        // Sort intervals by left endpoint
        intervals.sort_unstable_by(|a, b| a[0].cmp(&b[0]));

        // (original_index, query_value)
        let mut sorted_queries: Vec<(usize, i32)> = queries.into_iter()
            .enumerate()
            .collect();
        sorted_queries.sort_unstable_by(|a, b| a.1.cmp(&b.1));

        // Min-heap: (interval_length, right_endpoint)
        let mut heap: BinaryHeap<Reverse<(i32, i32)>> = BinaryHeap::new();
        let mut ans = vec![-1; sorted_queries.len()];
        let mut interval_idx = 0;

        for (q_idx, q_val) in sorted_queries {
            // Add all intervals whose left endpoint <= query value
            while interval_idx < intervals.len() && intervals[interval_idx][0] <= q_val {
                let left = intervals[interval_idx][0];
                let right = intervals[interval_idx][1];
                heap.push(Reverse((right - left + 1, right)));
                interval_idx += 1;
            }

            // Discard intervals that ended before the query value
            while let Some(&Reverse((_, right))) = heap.peek() {
                if right < q_val {
                    heap.pop();
                } else {
                    break;
                }
            }

            // The heap top is the shortest valid interval
            if let Some(&Reverse((len, _))) = heap.peek() {
                ans[q_idx] = len;
            }
        }

        ans
    }
}
```
