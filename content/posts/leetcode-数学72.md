---
title: "leetcode-数学72"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 数位和等于下标的最小下标

给你一个整数数组 nums 。

返回满足 nums[i] 的数位和（每一位数字相加求和）等于 i 的 最小 下标 i 。

如果不存在满足要求的下标，返回 -1 。




```
impl Solution {
    pub fn smallest_index(nums: Vec<i32>) -> i32 {
        // 从左到右遍历，第一个满足"数位和 == 下标"的位置即为最小下标。
        for (i, mut num) in nums.into_iter().enumerate() {
            let mut digit_sum = 0;
            while num > 0 {
                digit_sum += num % 10;
                num /= 10;
            }
            if digit_sum == i as i32 {
                return i as i32;
            }
        }
        -1
    }
}
```
