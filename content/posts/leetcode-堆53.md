---
title: "leetcode-堆53"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最大子序列的分数

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，两者长度都是 n ，再给你一个正整数 k 。你必须从 nums1 中选一个长度为 k 的 子序列 对应的下标。

对于选择的下标 iˇ0 ，iˇ1 ，...， iˇ(k - 1) ，你的 分数 定义如下：

nums1 中下标对应元素求和，乘以 nums2 中下标对应元素的 最小值 。
用公式表示： (nums1[iˇ0] + nums1[iˇ1] +...+ nums1[iˇ(k - 1)]) * min(nums2[iˇ0] , nums2[iˇ1], ... ,nums2[iˇ(k - 1)]) 。
请你返回 最大 可能的分数。

一个数组的 子序列 下标是集合 {0, 1, ..., n-1} 中删除若干元素得到的剩余集合，也可以不删除任何元素。




```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    pub fn max_score(nums1: Vec<i32>, nums2: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        // 按 nums2 降序排列，同时保留对应的 nums1
        let mut pairs: Vec<(i64, i64)> = nums1
            .into_iter()
            .zip(nums2.into_iter())
            .map(|(a, b)| (a as i64, b as i64))
            .collect();
        pairs.sort_unstable_by(|a, b| b.1.cmp(&a.1));

        // 最小堆维护当前选中的 k 个 nums1 元素
        let mut heap = BinaryHeap::with_capacity(k);
        let mut sum = 0i64;

        // 先选前 k 个
        for i in 0..k {
            sum += pairs[i].0;
            heap.push(Reverse(pairs[i].0));
        }

        // 初始答案：前 k 个的 nums1 之和 × 第 k 个的 nums2（当前最小 nums2）
        let mut ans = sum * pairs[k - 1].1;

        // 尝试用后面的更大 nums2 替换堆中最小的 nums1
        for i in k..pairs.len() {
            let (n1, n2) = pairs[i];
            // 如果当前 nums1 比堆中最小的大，替换后可能更优
            if let Some(Reverse(min)) = heap.peek() {
                if n1 > *min {
                    sum += n1 - min;
                    heap.pop();
                    heap.push(Reverse(n1));
                    ans = ans.max(sum * n2);
                }
            }
        }

        ans
    }
}
```
