---
title: "leetcode-堆48"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 找出数组的第 K 大和

给你一个整数数组 nums 和一个 正 整数 k 。你可以选择数组的任一 子序列 并且对其全部元素求和。

数组的 第 k 大和 定义为：可以获得的第 k 个 最大 子序列和（子序列和允许出现重复）

返回数组的 第 k 大和 。

子序列是一个可以由其他数组删除某些或不删除元素派生而来的数组，且派生过程不改变剩余元素的顺序。

注意：空子序列的和视作 0 。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    /// 第 k 大和：最大子序列和 = total_positive - 第 k 小"惩罚值"
    ///
    /// 核心思想：将所有正数选入基准和，用最小堆枚举"去掉某些正数 / 加入某些负数"
    /// 产生的惩罚值，第 k 小的惩罚值对应第 k 大的子序列和。
    pub fn k_sum(nums: Vec<i32>, k: i32) -> i64 {
        // 1. 计算全选正数的基准和，负数转为绝对值
        let mut abs: Vec<i64> = nums
            .into_iter()
            .map(|x| x as i64)
            .collect();

        let mut base = 0i64;
        for x in &mut abs {
            if *x >= 0 {
                base += *x;
            } else {
                *x = -*x;
            }
        }

        // 2. 按绝对值升序，用最小堆找第 k 小惩罚值
        abs.sort_unstable();

        // k=1 时直接返回 base（空惩罚）
        if k == 1 {
            return base;
        }

        let n = abs.len();
        // 堆元素: (惩罚值, 当前可用下标)
        let mut heap: BinaryHeap<(Reverse<i64>, usize)> = BinaryHeap::new();
        heap.push((Reverse(abs[0]), 0));

        let mut penalty = 0i64;
        for _ in 2..=k {
            let (Reverse(p), i) = heap.pop().unwrap();
            penalty = p;

            if i + 1 >= n {
                continue;
            }
            // 分支1: 继续往后加
            heap.push((Reverse(p + abs[i + 1]), i + 1));
            // 分支2: 替换当前选择
            heap.push((Reverse(p - abs[i] + abs[i + 1]), i + 1));
        }

        base - penalty
    }
}
```
