---
title: "leetcode-堆2"
date: 2026-08-21T09:54:10+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 查找和最小的 K 对数字

给定两个以 非递减顺序排列 的整数数组 nums1 和 nums2 , 以及一个整数 k 。

定义一对值 (u,v)，其中第一个元素来自 nums1，第二个元素来自 nums2 。

请找到和最小的 k 个数对 (u1,v1),  (u2,v2)  ...  (uk,vk) 。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 最小堆求两数组中前 k 个最小和数对
    ///
    /// 思路：初始将 nums1[i] 与 nums2[0] 配对入堆（最多 k 个）。
    /// 每次弹出和最小的数对 (i, j)，然后将 (i, j+1) 入堆，
    /// 保证同一行按列递增顺序被访问，不会遗漏更小的和。
    pub fn k_smallest_pairs(nums1: Vec<i32>, nums2: Vec<i32>, mut k: i32) -> Vec<Vec<i32>> {
        let m = nums1.len().min(k as usize);
        let n = nums2.len();
        if m == 0 || n == 0 { return vec![]; }

        // Rust 的 BinaryHeap 默认最大堆，用负数模拟最小堆
        let mut heap: BinaryHeap<(i32, usize, usize)> = (0..m)
            .map(|i| (-nums1[i] - nums2[0], i, 0))
            .collect();

        let mut ans = Vec::with_capacity(k as usize);

        while let Some((_, i, j)) = heap.pop() {
            if k == 0 { break; }
            k -= 1;
            ans.push(vec![nums1[i], nums2[j]]);

            if j + 1 < n {
                heap.push((-nums1[i] - nums2[j + 1], i, j + 1));
            }
        }

        ans
    }
}
```
