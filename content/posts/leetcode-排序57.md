---
title: "leetcode-排序57"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 找出分区值

给你一个 正 整数数组 nums 。

将 nums 分成两个数组：nums1 和 nums2 ，并满足下述条件：

数组 nums 中的每个元素都属于数组 nums1 或数组 nums2 。
两个数组都 非空 。
分区值 最小 。

分区值的计算方法是 |max(nums1) - min(nums2)| 。

其中，max(nums1) 表示数组 nums1 中的最大元素，min(nums2) 表示数组 nums2 中的最小元素。

返回表示分区值的整数。


```
impl Solution {
    /// 返回将 nums 分成两个非空数组后的最小分区值。
    ///
    /// 思路：
    /// 分区值为 |max(nums1) - min(nums2)|。将数组排序后，
    /// 最优划分一定是在某对相邻元素之间切开，使 nums1 为左半部分、
    /// nums2 为右半部分，此时分区值即相邻元素之差。取所有相邻差的最小值即可。
    pub fn find_value_of_partition(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();
        nums.windows(2)
            .map(|w| w[1] - w[0])
            .min()
            .unwrap()
    }
}
```
