---
title: "leetcode-堆37"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 移除石子使总数最小

给你一个整数数组 piles ，数组 下标从 0 开始 ，其中 piles[i] 表示第 i 堆石子中的石子数量。另给你一个整数 k ，请你执行下述操作 恰好 k 次：

选出任一石子堆 piles[i] ，并从中 移除 floor(piles[i] / 2) 颗石子。
注意：你可以对 同一堆 石子多次执行此操作。

返回执行 k 次操作后，剩下石子的 最小 总数。

floor(x) 为 小于 或 等于 x 的 最大 整数。（即，对 x 向下取整）。


```
use std::collections::BinaryHeap;

impl Solution {
    pub fn min_stone_sum(piles: Vec<i32>, k: i32) -> i32 {
        let mut heap = BinaryHeap::from(piles);

        for _ in 0..k {
            let max = match heap.pop() {
                Some(x) => x,
                None => break,
            };
            // Removing floor(x/2) from x leaves (x + 1) / 2
            heap.push((max + 1) / 2);
        }

        heap.iter().sum()
    }
}
```
