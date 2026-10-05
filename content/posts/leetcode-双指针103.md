---
title: "leetcode-双指针103"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 统计和小于目标的下标对数目

给你一个下标从 0 开始长度为 n 的整数数组 nums 和一个整数 target ，请你返回满足 0 <= i < j < n 且 nums[i] + nums[j] < target 的下标对 (i, j) 的数目。


```
impl Solution {
    /// 统计满足 nums[i] + nums[j] < target 的下标对 (i, j) 数量。
    ///
    /// 思路：先排序，再用双指针从两端向中间收缩。
    /// 若 nums[left] + nums[right] < target，则 right 与 [left+1, right] 中任意元素配对均满足，
    /// 一次性累加 right - left 个，然后 left 右移；否则 right 左移。
    /// 时间复杂度 O(n log n)，空间复杂度 O(1)。
    pub fn count_pairs(mut nums: Vec<i32>, target: i32) -> i32 {
        nums.sort_unstable();

        let mut ans = 0;
        let mut left = 0;
        let mut right = nums.len() - 1;

        while left < right {
            if nums[left] + nums[right] < target {
                ans += right - left;
                left += 1;
            } else {
                right -= 1;
            }
        }

        ans as i32
    }
}
```
