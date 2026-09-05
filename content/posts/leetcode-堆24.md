---
title: "leetcode-堆24"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 有序矩阵中的第 k 个最小数组和

给你一个 m * n 的矩阵 mat，以及一个整数 k ，矩阵中的每一行都以非递减的顺序排列。

你可以从每一行中选出 1 个元素形成一个数组。返回所有可能数组中的第 k 个 最小 数组和。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 逐行合并：每轮将所有已有和加上当前行元素，用最大堆保留最小的 k 个
    pub fn kth_smallest(mat: Vec<Vec<i32>>, k: i32) -> i32 {
        let k = k as usize;
        let mut sums = BinaryHeap::from([0]);

        for row in &mat {
            let mut next = BinaryHeap::new();
            while let Some(sum) = sums.pop() {
                for &val in row {
                    next.push(sum + val);
                    if next.len() > k {
                        next.pop(); // 弹出最大的，只保留 k 个最小的
                    }
                }
            }
            sums = next;
        }

        sums.pop().unwrap()
    }
}
```
