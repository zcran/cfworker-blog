---
title: "leetcode-模拟88"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 标记所有元素后数组的分数

给你一个数组 nums ，它包含若干正整数。

一开始分数 score = 0 ，请你按照下面算法求出最后分数：

从数组中选择最小且没有被标记的整数。如果有相等元素，选择下标最小的一个。
将选中的整数加到 score 中。
标记 被选中元素，如果有相邻元素，则同时标记 与它相邻的两个元素 。
重复此过程直到数组中所有元素都被标记。

请你返回执行上述算法后最后的分数。


```
use std::collections::BinaryHeap;

impl Solution {
    pub fn find_score(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        let mut marked = vec![false; n];
        // 最小堆：存储 (值, 下标)，用 Reverse 实现最小堆
        let mut heap: BinaryHeap<std::cmp::Reverse<(i32, usize)>> = nums
            .into_iter()
            .enumerate()
            .map(|(i, v)| std::cmp::Reverse((v, i)))
            .collect();

        let mut score = 0i64;

        while let Some(std::cmp::Reverse((val, idx))) = heap.pop() {
            if marked[idx] {
                continue;
            }
            score += val as i64;
            marked[idx] = true;
            // 标记相邻元素，边界安全
            if idx > 0 {
                marked[idx - 1] = true;
            }
            if idx + 1 < n {
                marked[idx + 1] = true;
            }
        }

        score
    }
}
```
