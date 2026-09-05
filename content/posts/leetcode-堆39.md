---
title: "leetcode-堆39"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 找到和最大的长度为 K 的子序列

给你一个整数数组 nums 和一个整数 k 。你需要找到 nums 中长度为 k 的 子序列 ，且这个子序列的 和最大 。

请你返回 任意 一个长度为 k 的整数子序列。

子序列 定义为从一个数组里删除一些元素后，不改变剩下元素的顺序得到的数组。


```
impl Solution {
    pub fn max_subsequence(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let k = k as usize;

        // Pair each value with its original index
        let mut indexed: Vec<(usize, i32)> = nums.into_iter()
            .enumerate()
            .collect();

        // Partition to find the k largest by value (O(n) average)
        indexed.select_nth_unstable_by(k - 1, |a, b| b.1.cmp(&a.1));

        // Sort the top k by original index to restore subsequence order
        indexed[..k].sort_unstable_by_key(|&(idx, _)| idx);

        // Extract values
        indexed.into_iter()
            .take(k)
            .map(|(_, val)| val)
            .collect()
    }
}
```
