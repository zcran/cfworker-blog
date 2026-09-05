---
title: "leetcode-堆63"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 选出和最大的 K 个元素

给你两个整数数组，nums1 和 nums2，长度均为 n，以及一个正整数 k 。

对从 0 到 n - 1 每个下标 i ，执行下述操作：

找出所有满足 nums1[j] 小于 nums1[i] 的下标 j 。

从这些下标对应的 nums2[j] 中选出 至多 k 个，并 最大化 这些值的总和作为结果。

返回一个长度为 n 的数组 answer ，其中 answer[i] 表示对应下标 i 的结果。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn find_max_sum(nums1: Vec<i32>, nums2: Vec<i32>, k: i32) -> Vec<i64> {
        let k = k as usize;
        let n = nums1.len();
        let mut ans = vec![0i64; n];

        // 按 nums1 升序排列，同时保留原下标
        let mut order: Vec<_> = nums1.into_iter()
            .zip(nums2)
            .enumerate()
            .map(|(i, (a, b))| (a, b, i))
            .collect();
        order.sort_unstable_by_key(|x| x.0);

        // 维护一个大小不超过 k 的最小堆，保存当前最大的 k 个 nums2
        let mut heap = BinaryHeap::with_capacity(k + 1);
        let mut sum = 0i64;

        let mut i = 0;
        while i < n {
            let val = order[i].0;

            // 所有 nums1 等于 val 的位置，答案相同（来自前面更小的 nums1 对应的 nums2）
            while i < n && order[i].0 == val {
                ans[order[i].2] = sum;
                i += 1;
            }

            // 将这些位置的 nums2 加入堆
            let start = i - (order[..i].iter().rev().take_while(|x| x.0 == val).count());
            for j in start..i {
                let y = order[j].1 as i64;
                sum += y;
                heap.push(Reverse(y));
                if heap.len() > k {
                    if let Some(Reverse(min)) = heap.pop() {
                        sum -= min;
                    }
                }
            }
        }

        ans
    }
}
```
