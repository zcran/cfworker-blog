---
title: "leetcode-双指针60"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 有序数组的平方

给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。


```
impl Solution {
    pub fn sorted_squares(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut result = vec![0; n];
        let mut left = 0;
        let mut right = n - 1;
        let mut pos = n - 1;

        while left <= right {
            let left_sq = nums[left] * nums[left];
            let right_sq = nums[right] * nums[right];

            if left_sq > right_sq {
                result[pos] = left_sq;
                left += 1;
            } else {
                result[pos] = right_sq;
                right -= 1;
            }

            // 只有 pos > 0 时才递减，避免下溢
            if pos == 0 {
                break;
            }
            pos -= 1;
        }

        result
    }
}
```
O(n)

```
impl Solution {
    pub fn sorted_squares(nums: Vec<i32>) -> Vec<i32> {
        let mut ans: Vec<i32> = nums.iter().map(|&num| num * num).collect();
        ans.sort();
        ans
    }
}
```
O(nlog(n))
