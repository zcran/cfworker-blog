---
title: "leetcode-数组27"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 最大升序子数组和

给你一个正整数组成的数组 nums ，返回 nums 中一个 严格递增子数组 的最大可能元素和。

子数组是数组中的一个连续数字序列。


```
impl Solution {
    /// 求严格递增子数组的最大元素和。
    ///
    /// 一次遍历，维护当前递增子数组的和 `cur` 与历史最大和 `max`：
    /// - 若 `nums[i] > nums[i-1]`，累加到 `cur`；
    /// - 否则以 `nums[i]` 重新开始一段。
    /// 每轮用 `cur` 更新 `max`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn max_ascending_sum(nums: Vec<i32>) -> i32 {
        let mut max = nums[0];
        let mut cur = nums[0];

        for i in 1..nums.len() {
            if nums[i] > nums[i - 1] {
                cur += nums[i];
            } else {
                cur = nums[i];
            }
            max = max.max(cur);
        }

        max
    }
}
```
