---
title: "leetcode-贪心135"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 移除后集合的最多元素数

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，它们的长度都是偶数 n 。

你必须从 nums1 中移除 n / 2 个元素，同时从 nums2 中也移除 n / 2 个元素。移除之后，你将 nums1 和 nums2 中剩下的元素插入到集合 s 中。

返回集合 s可能的 最多 包含多少元素。


```
use std::collections::HashSet;

impl Solution {
    pub fn maximum_set_size(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        // 每个数组长度 n，需各自删掉 n/2 个元素，即各自保留 n/2 个。
        // 集合 s 中的元素来自两数组保留部分之并。
        //
        // 记：
        //   only1 = 仅出现在 nums1 的元素数
        //   only2 = 仅出现在 nums2 的元素数
        //   common = 两数组共有的元素数
        //
        // 两数组各自最多保留 n/2 个，因此 from1 = min(only1, n/2)，
        // from2 = min(only2, n/2)。再加上 common 中的元素（只要有一侧保留即可贡献），
        // 但总数不超过 n（两数组元素并集大小）。
        let n = nums1.len() as i32;
        let half = n / 2;

        let set1: HashSet<i32> = nums1.into_iter().collect();
        let set2: HashSet<i32> = nums2.into_iter().collect();

        let common = set1.intersection(&set2).count() as i32;
        let only1 = set1.len() as i32 - common;
        let only2 = set2.len() as i32 - common;

        (only1.min(half) + only2.min(half) + common).min(n)
    }
}
```
