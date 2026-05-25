---
title: "leetcode-树状树组32"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 找到 Alice 和 Bob 可以相遇的建筑

给你一个下标从 0 开始的正整数数组 heights ，其中 heights[i] 表示第 i 栋建筑的高度。

如果一个人在建筑 i ，且存在 i < j 的建筑 j 满足 heights[i] < heights[j] ，那么这个人可以移动到建筑 j 。

给你另外一个数组 queries ，其中 queries[i] = [ai, bi] 。第 i 个查询中，Alice 在建筑 ai ，Bob 在建筑 bi 。

请你能返回一个数组 ans ，其中 ans[i] 是第 i 个查询中，Alice 和 Bob 可以相遇的 最左边的建筑 。如果对于查询 i ，Alice 和 Bob 不能相遇，令 ans[i] 为 -1 。

```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 对于每个查询 (a, b)，找到最小的索引 k ≥ max(a, b)，
    /// 使得可以从 a 和 b 同时跳到 k（即 heights[k] > max(heights[a], heights[b])）。
    /// 如果可以直接互相到达（比如 a < b 且 heights[a] < heights[b]），则答案为 b。
    pub fn leftmost_building_queries(heights: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = heights.len();
        let qn = queries.len();
        // 按右端点（即 max(i, j)）存储未解决的查询：每个元素是 (所需最小高度, 查询索引)
        let mut pending: Vec<Vec<(i32, usize)>> = vec![vec![]; n];
        let mut answer = vec![-1; qn];

        // 预处理查询：处理可直接回答的情况，否则加入待处理队列
        for (qi, q) in queries.iter().enumerate() {
            let i = q[0] as usize;
            let j = q[1] as usize;
            if i == j {
                answer[qi] = i as i32;
            } else if i < j && heights[i] < heights[j] {
                answer[qi] = j as i32;
            } else if i > j && heights[i] > heights[j] {
                answer[qi] = i as i32;
            } else {
                // 无法直接到达，需要寻找共同跳跃点
                let left = i.min(j);
                let right = i.max(j);
                let need_height = heights[i].max(heights[j]);
                pending[right].push((need_height, qi));
            }
        }

        // 最小堆：存储 (所需高度, 查询索引)，按所需高度升序
        let mut heap = BinaryHeap::new();

        // 从左到右扫描建筑，维护堆中等待跳跃的查询
        for idx in 0..n {
            // 当堆顶的所需高度小于当前建筑高度时，该查询可以在此建筑解决
            while let Some(&Reverse((need, q_idx))) = heap.peek() {
                if need < heights[idx] {
                    answer[q_idx] = idx as i32;
                    heap.pop();
                } else {
                    break;
                }
            }
            // 将当前索引作为右端点的所有查询加入堆
            for &(need, q_idx) in &pending[idx] {
                heap.push(Reverse((need, q_idx)));
            }
        }

        answer
    }
}
```
