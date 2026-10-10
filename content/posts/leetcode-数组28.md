---
title: "leetcode-数组28"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 到目标元素的最小距离

给你一个整数数组 nums （下标 从 0 开始 计数）以及两个整数 target 和 start ，请你找出一个下标 i ，满足 nums[i] == target 且 abs(i - start) 最小化 。注意：abs(x) 表示 x 的绝对值。

返回 abs(i - start) 。

题目数据保证 target 存在于 nums 中。


```
impl Solution {
    /// 找与 `start` 最近的 `target` 下标，返回 `abs(i - start)`。
    ///
    /// 遍历数组，遇到 `nums[i] == target` 时用 `abs(i - start)` 更新最小值。
    /// 由于距离非负，初始值可设为 `i32::MAX`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn get_min_distance(nums: Vec<i32>, target: i32, start: i32) -> i32 {
        let mut min_dist = i32::MAX;

        for (i, &x) in nums.iter().enumerate() {
            if x == target {
                min_dist = min_dist.min((i as i32 - start).abs());
            }
        }

        min_dist
    }
}
```
