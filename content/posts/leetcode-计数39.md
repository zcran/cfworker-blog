---
title: "leetcode-计数39"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 通过最少操作次数使数组的和相等

给你两个长度可能不等的整数数组 nums1 和 nums2 。两个数组中的所有值都在 1 到 6 之间（包含 1 和 6）。

每次操作中，你可以选择 任意 数组中的任意一个整数，将它变成 1 到 6 之间 任意 的值（包含 1 和 6）。

请你返回使 nums1 中所有数的和与 nums2 中所有数的和相等的最少操作次数。如果无法使两个数组的和相等，请返回 -1 。


```
impl Solution {
    pub fn min_operations(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let (n1, n2) = (nums1.len(), nums2.len());
        // 如果和的范围无法重叠，直接返回 -1
        if n1 * 6 < n2 || n2 * 6 < n1 {
            return -1;
        }

        let (mut sum1, mut sum2) = (nums1.iter().sum::<i32>(), nums2.iter().sum::<i32>());
        // 保证 sum1 >= sum2，将需要减少的数组作为目标
        let (mut to_reduce, mut to_increase) = if sum1 >= sum2 {
            (nums1, nums2)
        } else {
            (nums2, nums1)
        };
        let diff = (sum1 - sum2).abs();

        // 收集所有能产生的"变化量"：减少方可以减小的值，增加方可以增大的值
        let mut gains = Vec::with_capacity(to_reduce.len() + to_increase.len());
        for &v in &to_reduce {
            gains.push(v - 1); // 当前值可减少的量
        }
        for &v in &to_increase {
            gains.push(6 - v); // 当前值可增大的量
        }

        // 降序排序，优先使用最大的变化量
        gains.sort_unstable_by(|a, b| b.cmp(a));

        let mut need = diff;
        let mut ops = 0;
        for &gain in &gains {
            if need <= 0 { break; }
            need -= gain;
            ops += 1;
        }

        if need > 0 { -1 } else { ops }
    }
}
```
