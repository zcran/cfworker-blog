---
title: "leetcode-数组32"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 值相等的最小索引

给你一个下标从 0 开始的整数数组 nums ，返回 nums 中满足 i mod 10 == nums[i] 的最小下标 i ；如果不存在这样的下标，返回 -1 。

x mod y 表示 x 除以 y 的 余数 。


```
impl Solution {
    /// 找满足 `i % 10 == nums[i]` 的最小下标，不存在则返回 -1。
    ///
    /// 从左到右遍历，第一个满足条件的下标即为最小下标。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn smallest_equal(nums: Vec<i32>) -> i32 {
        nums.iter()
            .enumerate()
            .find(|&(i, &x)| i as i32 % 10 == x)
            .map_or(-1, |(i, _)| i as i32)
    }
}
```
