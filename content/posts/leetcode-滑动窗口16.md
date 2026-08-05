---
title: "leetcode-滑动窗口16"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 乘积小于 K 的子数组

给你一个整数数组 nums 和一个整数 k ，请你返回子数组内所有元素的乘积严格小于 k 的连续子数组的数目。


```
impl Solution {
    pub fn num_subarray_product_less_than_k(nums: Vec<i32>, k: i32) -> i32 {
        if k <= 1 {
            return 0;
        }

        let mut count = 0;
        let mut product = 1;
        let mut left = 0;

        for (right, &num) in nums.iter().enumerate() {
            product *= num;

            // 乘积过大时，收缩左边界
            while product >= k {
                product /= nums[left];
                left += 1;
            }

            // 以 right 为结尾的子数组数量 = right - left + 1
            count += right - left + 1;
        }

        count as i32
    }
}
```
