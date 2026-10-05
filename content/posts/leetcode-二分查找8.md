---
title: "leetcode-二分查找8"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 寻找峰值

峰值元素是指其值严格大于左右相邻值的元素。

给你一个整数数组 nums，找到峰值元素并返回其索引。数组可能包含多个峰值，在这种情况下，返回 任何一个峰值 所在位置即可。

你可以假设 nums[-1] = nums[n] = -∞ 。

你必须实现时间复杂度为 O(log n) 的算法来解决此问题。


```
impl Solution {
    pub fn find_peak_element(nums: Vec<i32>) -> i32 {
        // 只需沿上坡方向移动，最终必然到达某个峰值。
        // 在左闭右开区间 [left, right) 中二分
        let (mut left, mut right) = (0, nums.len() - 1);
        while left < right {
            let mid = left + (right - left) / 2;
            if nums[mid] > nums[mid + 1] {
                // 下坡：峰值在 [left, mid]
                right = mid;
            } else {
                // 上坡：峰值在 [mid+1, right]
                left = mid + 1;
            }
        }
        left as i32
    }
}
```
