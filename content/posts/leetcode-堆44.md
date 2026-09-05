---
title: "leetcode-堆44"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## K 次增加后的最大乘积

给你一个非负整数数组 nums 和一个整数 k 。每次操作，你可以选择 nums 中 任一 元素并将它 增加 1 。

请你返回 至多 k 次操作后，能得到的 nums的 最大乘积 。由于答案可能很大，请你将答案对 10^9 + 7 取余后返回。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn maximum_product(nums: Vec<i32>, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // Min-heap: always increment the smallest number
        let mut heap: BinaryHeap<Reverse<i64>> = nums
            .into_iter()
            .map(|x| Reverse(x as i64))
            .collect();

        for _ in 0..k {
            let Reverse(min) = heap.pop().unwrap();
            heap.push(Reverse(min + 1));
        }

        heap.into_iter().fold(1i64, |prod, Reverse(val)| prod * val % MOD) as i32
    }
}
```
