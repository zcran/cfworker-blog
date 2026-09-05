---
title: "leetcode-堆62"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 提取至多 K 个元素的最大总和

给你一个大小为 n x m 的二维矩阵 grid ，以及一个长度为 n 的整数数组 limits ，和一个整数 k 。你的目标是从矩阵 grid 中提取出 至多 k 个元素，并计算这些元素的最大总和，提取时需满足以下限制：

从 grid 的第 i 行提取的元素数量不超过 limits[i] 。

返回最大总和。


```
use std::collections::BinaryHeap;

impl Solution {
    pub fn max_sum(grid: Vec<Vec<i32>>, limits: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        if k == 0 {
            return 0;
        }

        // 每行取前 limits[i] 大的元素，汇总到一个堆里
        let mut heap = BinaryHeap::new();
        for (row, &lim) in grid.into_iter().zip(limits.iter()) {
            let take = lim.min(row.len() as i32) as usize;
            if take == 0 {
                continue;
            }
            let mut row = row;
            row.sort_unstable_by(|a, b| b.cmp(a));
            heap.extend(row.into_iter().take(take));
        }

        // 弹出最大的 k 个求和
        (0..k).filter_map(|_| heap.pop()).map(|v| v as i64).sum()
    }
}
```
