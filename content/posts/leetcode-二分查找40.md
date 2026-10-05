---
title: "leetcode-二分查找40"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 特殊数组的特征值

给你一个非负整数数组 nums 。如果存在一个数 x ，使得 nums 中恰好有 x 个元素 大于或者等于 x ，那么就称 nums 是一个 特殊数组 ，而 x 是该数组的 特征值 。

注意： x 不必 是 nums 的中的元素。

如果数组 nums 是一个 特殊数组 ，请返回它的特征值 x 。否则，返回 -1 。可以证明的是，如果 nums 是特殊数组，那么其特征值 x 是 唯一的 。




```
impl Solution {
    /// 返回特殊数组的特征值 x，若不存在则返回 -1。
    ///
    /// 特征值 x 满足：nums 中恰好有 x 个元素 >= x。
    /// 思路：排序后枚举 x（1..=n）。
    ///   排序后，若恰好有 i 个元素 >= i，则需满足：
    ///     - nums[n - i] >= i（第 i 大的元素 >= i，即后 i 个都满足）
    ///     - n - i == 0 或 nums[n - i - 1] < i（前一个元素 < i，保证恰好 i 个）
    pub fn special_array(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();
        let n = nums.len();

        for i in 1..=n {
            let j = n - i; // 从下标 j 开始共 i 个元素
            if nums[j] >= i as i32 && (j == 0 || nums[j - 1] < i as i32) {
                return i as i32;
            }
        }

        -1
    }
}
```
