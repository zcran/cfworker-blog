---
title: "leetcode-堆42"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 将数组和减半的最少操作次数

给你一个正整数数组 nums 。每一次操作中，你可以从 nums 中选择 任意 一个数并将它减小到 恰好 一半。（注意，在后续操作中你可以对减半过的数继续执行操作）

请你返回将 nums 数组和 至少 减少一半的 最少 操作数。


```
use std::collections::BinaryHeap;
use std::cmp::Ordering;

// Wrapper to impose total ordering on f64 (safe because all values are positive, no NaN)
#[derive(PartialOrd, PartialEq)]
struct F64(f64);

impl Eq for F64 {}

impl Ord for F64 {
    fn cmp(&self, other: &Self) -> Ordering {
        self.partial_cmp(other).unwrap()
    }
}

impl Solution {
    pub fn halve_array(nums: Vec<i32>) -> i32 {
        let mut heap: BinaryHeap<F64> = BinaryHeap::new();
        let mut total = 0.0;

        for x in nums {
            total += x as f64;
            heap.push(F64(x as f64));
        }

        let target = total / 2.0;
        let mut reduced = 0.0;
        let mut ops = 0;

        while reduced < target {
            let F64(val) = heap.pop().unwrap();
            let half = val / 2.0;
            reduced += half;
            ops += 1;
            heap.push(F64(half));
        }

        ops
    }
}
```
