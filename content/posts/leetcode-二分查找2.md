---
title: "leetcode-二分查找2"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 在排序数组中查找元素的第一个和最后一个位置

给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题。


```
impl Solution {
    /// 二分查找目标值的边界。
    /// lower = true  时找第一个 >= target 的位置（左边界）
    /// lower = false 时找第一个 >  target 的位置（右边界 + 1）
    fn bound(nums: &[i32], target: i32, lower: bool) -> usize {
        let (mut left, mut right) = (0, nums.len()); // 左闭右开区间 [left, right)
        while left < right {
            let mid = left + (right - left) / 2;
            if nums[mid] > target || (lower && nums[mid] == target) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        left
    }

    pub fn search_range(nums: Vec<i32>, target: i32) -> Vec<i32> {
        let start = Self::bound(&nums, target, true);
        // 若 start 越界或未命中，说明目标不存在
        if start == nums.len() || nums[start] != target {
            return vec![-1, -1];
        }
        let end = Self::bound(&nums, target, false) - 1;
        vec![start as i32, end as i32]
    }
}
```
