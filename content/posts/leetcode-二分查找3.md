---
title: "leetcode-二分查找3"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 搜索插入位置

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 O(log n) 的算法。


```
impl Solution {
    /// 返回最小的满足 nums[i] >= target 的下标 i（即 lower_bound）。
    /// 若数组为空或所有元素都 < target，则返回 nums.len()。
    /// 前提：nums 非递减。
    fn lower_bound(nums: &[i32], target: i32) -> usize {
        let (mut left, mut right) = (0, nums.len()); // 左闭右开区间 [left, right)
        // 循环不变量：nums[left-1] < target <= nums[right]
        while left < right {
            let mid = left + (right - left) / 2;
            if nums[mid] < target {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        left
    }

    pub fn search_insert(nums: Vec<i32>, target: i32) -> i32 {
        Self::lower_bound(&nums, target) as i32
    }
}
```
