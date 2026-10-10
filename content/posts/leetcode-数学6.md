---
title: "leetcode-数学6"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 全局倒置与局部倒置

给你一个长度为 n 的整数数组 nums ，表示由范围 [0, n - 1] 内所有整数组成的一个排列。

全局倒置 的数目等于满足下述条件不同下标对 (i, j) 的数目：

0 <= i < j < n
nums[i] > nums[j]

局部倒置 的数目等于满足下述条件的下标 i 的数目：

0 <= i < n - 1
nums[i] > nums[i + 1]

当数组 nums 中 全局倒置 的数量等于 局部倒置 的数量时，返回 true ；否则，返回 false 。


```
impl Solution {
    /// 判断数组是否为「理想排列」，即全局倒置数等于局部倒置数。
    ///
    /// 关键观察：
    /// - 局部倒置（相邻逆序）一定是全局倒置，所以全局倒置 ≥ 局部倒置。
    /// - 要让两者相等，数组中不能存在「非相邻」的逆序对，
    ///   即对任意 i < j-1，都必须有 nums[i] < nums[j]。
    /// - 等价于：对每个位置 i ≥ 2，前面所有元素的最大值必须小于 nums[i]。
    ///
    /// 因此只需一次遍历，维护到 i-2 为止的最大值，检查是否小于 nums[i]。
    pub fn is_ideal_permutation(nums: Vec<i32>) -> bool {
        let mut max_so_far = 0;
        for i in 2..nums.len() {
            max_so_far = max_so_far.max(nums[i - 2]);
            if max_so_far > nums[i] {
                return false;
            }
        }
        true
    }
}
```
