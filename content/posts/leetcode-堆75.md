---
title: "leetcode-堆75"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 查找和最小的 K 对数字

给定两个以升序排列的整数数组 nums1 和 nums2 , 以及一个整数 k 。

定义一对值 (u,v)，其中第一个元素来自 nums1，第二个元素来自 nums2 。

请找到和最小的 k 个数对 (u1,v1),  (u2,v2)  ...  (uk,vk) 。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

/// 查找和最小的 k 个数对
///
/// 核心思路：使用最小堆维护候选数对
/// 1. 初始将所有 nums1[i] + nums2[0] 入堆
/// 2. 每次弹出最小的数对，然后将同一个 nums1[i] 与 nums2 的下一个元素组合入堆
/// 3. 时间复杂度 O(k log n1)，空间复杂度 O(n1)
impl Solution {
    pub fn k_smallest_pairs(nums1: Vec<i32>, nums2: Vec<i32>, k: i32) -> Vec<Vec<i32>> {
        let k = k as usize;
        let n1 = nums1.len();
        let n2 = nums2.len();

        // 如果任一数组为空，返回空结果
        if n1 == 0 || n2 == 0 || k == 0 {
            return Vec::new();
        }

        // 结果集，最多 k 对
        let mut result = Vec::with_capacity(k.min(n1 * n2));

        // 最小堆：存储 (和, nums1索引, nums2索引)
        // 使用 Reverse 实现最小堆
        let mut heap = BinaryHeap::with_capacity(n1);

        // 初始将 (nums1[i] + nums2[0], i, 0) 入堆
        for i in 0..n1 {
            heap.push(Reverse((nums1[i] + nums2[0], i, 0)));
        }

        // 循环直到找到 k 对或堆为空
        while let Some(Reverse((_sum, i, j))) = heap.pop() {
            result.push(vec![nums1[i], nums2[j]]);

            // 如果已经找到 k 对，提前退出
            if result.len() >= k {
                break;
            }

            // 将同一 nums1[i] 与 nums2 的下一个元素组合入堆
            let next_j = j + 1;
            if next_j < n2 {
                heap.push(Reverse((nums1[i] + nums2[next_j], i, next_j)));
            }
        }

        result
    }
}
```
