---
title: "leetcode-二分查找50"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 两个有序数组的第 K 小乘积

给你两个 从小到大排好序 且下标从 0 开始的整数数组 nums1 和 nums2 以及一个整数 k ，请你返回第 k （从 1 开始编号）小的 nums1[i] * nums2[j] 的乘积，其中 0 <= i < nums1.length 且 0 <= j < nums2.length 。


```
impl Solution {
    // 统计 nums2 中有多少个数与 x 的乘积 <= v
    // nums2 已升序，x 的符号决定乘积单调性，据此二分
    fn count_le(nums2: &[i32], x: i64, v: i64) -> i64 {
        let n = nums2.len();
        let (mut lo, mut hi) = (0i64, n as i64);
        while lo < hi {
            let mid = (lo + hi) / 2;
            let prod = x * nums2[mid as usize] as i64;
            // x >= 0 时乘积随下标递增，找最后一个 prod <= v
            // x < 0 时乘积随下标递减，找第一个 prod <= v
            if (x >= 0 && prod <= v) || (x < 0 && prod > v) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        if x >= 0 { lo } else { n as i64 - lo }
    }

    pub fn kth_smallest_product(nums1: Vec<i32>, nums2: Vec<i32>, k: i64) -> i64 {
        // 乘积范围：|nums1[i]|, |nums2[j]| <= 1e5，乘积 <= 1e10
        let (mut lo, mut hi) = (-(1e10 as i64), 1e10 as i64);
        while lo < hi {
            let mid = lo + (hi - lo) / 2;
            // 统计所有乘积 <= mid 的对数
            let mut cnt = 0i64;
            for &x in &nums1 {
                cnt += Self::count_le(&nums2, x as i64, mid);
            }
            if cnt < k {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        lo
    }
}
```
