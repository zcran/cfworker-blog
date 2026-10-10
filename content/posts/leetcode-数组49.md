---
title: "leetcode-数组49"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 找出与数组相加的整数 I

给你两个长度相等的数组 nums1 和 nums2。

数组 nums1 中的每个元素都与变量 x 所表示的整数相加。如果 x 为负数，则表现为元素值的减少。

在与 x 相加后，nums1 和 nums2 相等 。当两个数组中包含相同的整数，并且这些整数出现的频次相同时，两个数组 相等 。

返回整数 x 。




```
impl Solution {
    /// 求整数 `x`，使得 `nums1` 每个元素加 `x` 后与 `nums2` 相等。
    ///
    /// 两个数组长度相等，且每个元素都加同一个 `x`，
    /// 因此它们的最大值也相差 `x`，即 `x = max(nums2) - max(nums1)`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn added_integer(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let max1 = nums1.iter().max().unwrap();
        let max2 = nums2.iter().max().unwrap();
        max2 - max1
    }
}
```
