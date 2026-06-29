---
title: "leetcode-单调栈34"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---

## 子数组范围和


给你一个整数数组 nums 。nums 中，子数组的 范围 是子数组中最大元素和最小元素的差值。

返回 nums 中 所有 子数组范围的 和 。

子数组是数组中一个连续 非空 的元素序列。

```

impl Solution {
    pub fn sub_array_ranges(nums: Vec<i32>) -> i64 {
        let n = nums.len();

        // 计算所有子数组的最大值之和
        let sum_max = Self::sum_of_subarray_maxs(&nums);
        // 计算所有子数组的最小值之和
        let sum_min = Self::sum_of_subarray_mins(&nums);

        sum_max - sum_min
    }

    // 计算所有子数组的最大值之和
    fn sum_of_subarray_maxs(nums: &[i32]) -> i64 {
        let n = nums.len();
        let mut stack = Vec::new();
        let mut total = 0i64;

        for i in 0..=n {
            // 当遍历结束或当前元素小于栈顶元素时，计算以栈顶为最大值的子数组个数
            while !stack.is_empty() && (i == n || nums[*stack.last().unwrap()] < nums[i]) {
                let idx = stack.pop().unwrap();
                let left = if stack.is_empty() { -1 } else { *stack.last().unwrap() as i32 };
                let right = i as i32;

                // 左边能扩展的距离: idx - left
                // 右边能扩展的距离: right - idx
                let count = (idx as i32 - left) as i64 * (right - idx as i32) as i64;
                total += count * nums[idx] as i64;
            }
            stack.push(i);
        }

        total
    }

    // 计算所有子数组的最小值之和
    fn sum_of_subarray_mins(nums: &[i32]) -> i64 {
        let n = nums.len();
        let mut stack = Vec::new();
        let mut total = 0i64;

        for i in 0..=n {
            // 当遍历结束或当前元素小于等于栈顶元素时，计算以栈顶为最小值的子数组个数
            while !stack.is_empty() && (i == n || nums[*stack.last().unwrap()] > nums[i]) {
                let idx = stack.pop().unwrap();
                let left = if stack.is_empty() { -1 } else { *stack.last().unwrap() as i32 };
                let right = i as i32;

                // 左边能扩展的距离: idx - left
                // 右边能扩展的距离: right - idx
                let count = (idx as i32 - left) as i64 * (right - idx as i32) as i64;
                total += count * nums[idx] as i64;
            }
            stack.push(i);
        }

        total
    }
}
```
