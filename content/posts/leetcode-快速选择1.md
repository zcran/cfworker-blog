---
title: "Leetcode 快速选择1"
date: 2023-12-19T15:49:31+08:00
tags: ["leetcode", "快速选择"]
draft: false
---

## 摆动排序 II

给你一个整数数组 nums，将它重新排列成 nums[0] < nums[1] > nums[2] < nums[3]... 的顺序。

你可以假设所有输入数组都可以得到满足题目要求的结果。

```
impl Solution {
    pub fn wiggle_sort(nums: &mut Vec<i32>) {
        let mut nums_copy = nums.clone();
        nums_copy.sort();
        let n = nums.len();
        let (mut p, mut q) = ((n - 1) / 2, n - 1);
        for i in 0..n {
            if (i & 1) == 1 {
                nums[i] = nums_copy[q];
                q -= 1;
            } else {
                nums[i] = nums_copy[p];
                p -= 1;
            };
        }
    }
}
```