---
title: "leetcode-双指针37"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 有效三角形的个数

给定一个包含非负整数的数组 nums ，返回其中可以组成三角形三条边的三元组个数。


```
impl Solution {
    pub fn triangle_number(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();
        let mut count = 0;

        // 固定最长边 c，用双指针找满足 a + b > c 的 a 和 b
        for c_idx in 2..nums.len() {
            let c = nums[c_idx];
            let mut left = 0;
            let mut right = c_idx - 1;

            while left < right {
                if nums[left] + nums[right] > c {
                    // left..right 之间的所有数都可以与 nums[right] 组成三角形
                    // 因为数组有序，nums[left..right-1] + nums[right] > c 也成立
                    count += right - left;
                    right -= 1;
                } else {
                    // nums[left] 太小，右移左指针
                    left += 1;
                }
            }
        }

        count as i32
    }
}
```
