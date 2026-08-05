---
title: "leetcode-滑动窗口19"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 和相同的二元子数组

给你一个二元数组 nums ，和一个整数 goal ，请你统计并返回有多少个和为 goal 的 非空 子数组。

子数组 是数组的一段连续部分。


```
impl Solution {
    pub fn num_subarrays_with_sum(nums: Vec<i32>, goal: i32) -> i32 {
        // at_most(s) 返回和 <= s 的子数组个数
        fn at_most(nums: &[i32], s: i32) -> i32 {
            if s < 0 {
                return 0;
            }
            let mut count = 0;
            let mut sum = 0;
            let mut left = 0;

            for (right, &num) in nums.iter().enumerate() {
                sum += num;
                while sum > s {
                    sum -= nums[left];
                    left += 1;
                }
                count += right - left + 1;
            }

            count as i32
        }

        at_most(&nums, goal) - at_most(&nums, goal - 1)
    }
}
```
