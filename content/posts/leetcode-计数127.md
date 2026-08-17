---
title: "leetcode-计数127"
date: 2026-08-01T10:40:58+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 通过交换使数组相等的最小花费

给你两个大小为 n 的整数数组 nums1 和 nums2。

你可以对这两个数组执行以下两种操作任意次：

在同一个数组内交换：选择两个下标 i 和 j。然后，选择交换 nums1[i] 和 nums1[j]，或者交换 nums2[i] 和 nums2[j]。此操作是 免费的。

在两个数组之间交换：选择一个下标 i。然后，交换 nums1[i] 和 nums2[i]。此操作 花费为 1。

返回一个整数，表示使 nums1 和 nums2 相同 的 最小花费。如果不可能做到，返回 -1。




```
impl Solution {
    /// 返回使 nums1 和 nums2 相同的最小花费。
    ///
    /// 思路：
    /// 1. 统计两个数组中每个数字的出现次数差。
    ///    若某数字在 nums1 中出现次数多于 nums2，则 diff > 0；
    ///    反之 diff < 0。
    /// 2. 若任意数字的 diff 为奇数，则无法平衡，返回 -1。
    /// 3. 所有正 diff 之和表示需要从 nums1 交换到 nums2 的总元素数，
    ///    每次跨数组交换可解决两个元素（一个出、一个入），故答案为总差值 / 2。
    pub fn min_cost(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        use std::collections::HashMap;

        let mut diff = HashMap::with_capacity(nums1.len() + nums2.len());
        for &x in &nums1 {
            *diff.entry(x).or_insert(0) += 1;
        }
        for &x in &nums2 {
            *diff.entry(x).or_insert(0) -= 1;
        }

        let mut total = 0;
        for &d in diff.values() {
            if d & 1 != 0 {
                return -1; // 奇数差值无法通过交换平衡
            }
            if d > 0 {
                total += d;
            }
        }

        total / 2
    }
}
```
