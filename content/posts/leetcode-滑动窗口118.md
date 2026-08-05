---
title: "leetcode-滑动窗口118"
date: 2026-07-18T11:02:36+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 乘积小于 K 的子数组

给定一个正整数数组 nums和整数 k ，请找出该数组内乘积小于 k 的连续的子数组的个数。


```
impl Solution {
    pub fn num_subarray_product_less_than_k(nums: Vec<i32>, k: i32) -> i32 {
        if k <= 1 {
            return 0; // 所有正整数乘积 >= 1，k <= 1 时无解
        }

        let mut prod = 1;
        let mut left = 0;
        let mut count = 0;

        for (right, &x) in nums.iter().enumerate() {
            prod *= x;

            // 收缩左边界，使乘积 < k
            while prod >= k && left <= right {
                prod /= nums[left];
                left += 1;
            }

            // 以 right 为右端点的子数组数量 = right - left + 1
            count += (right - left + 1) as i32;
        }

        count
    }
}
```
