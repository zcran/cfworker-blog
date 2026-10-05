---
title: "leetcode-双指针78"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 下标对中的最大距离

给你两个 非递增 的整数数组 nums1 和 nums2 ，数组下标均 从 0 开始 计数。

下标对 (i, j) 中 0 <= i < nums1.length 且 0 <= j < nums2.length 。如果该下标对同时满足 i <= j 且 nums1[i] <= nums2[j] ，则称之为 有效 下标对，该下标对的 距离 为 j - i 。

返回所有 有效 下标对 (i, j) 中的 最大距离 。如果不存在有效下标对，返回 0 。

一个数组 arr ，如果每个 1 <= i < arr.length 均有 arr[i-1] >= arr[i] 成立，那么该数组是一个 非递增 数组。


```
impl Solution {
    pub fn max_distance(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let (n1, n2) = (nums1.len(), nums2.len());
        let mut i = 0;
        let mut max_dist = 0;

        // 遍历 nums2 的每个位置作为右端点
        // 对于每个 j，找到最小的 i 使得 nums1[i] <= nums2[j]
        // 由于 nums1 非递增，i 单调不降，可以用双指针
        for j in 0..n2 {
            // 跳过所有不满足条件的 nums1 元素
            while i < n1 && nums1[i] > nums2[j] {
                i += 1;
            }

            // 如果存在满足条件的 i，更新最大距离
            if i < n1 {
                max_dist = max_dist.max(j as i32 - i as i32);
            }
        }

        max_dist
    }
}
```
