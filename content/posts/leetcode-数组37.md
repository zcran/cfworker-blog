---
title: "leetcode-数组37"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 全 0 子数组的数目

给你一个整数数组 nums ，返回全部为 0 的 子数组 数目。

子数组 是一个数组中一段连续非空元素组成的序列。


```
impl Solution {
    /// 统计全部由 0 组成的子数组数目。
    ///
    /// 一次遍历，用 `cur` 记录以当前位置结尾的连续 0 的个数：
    /// - 若 `nums[i] == 0`，则以 `i` 结尾的全 0 子数组有 `cur` 个，
    ///   累加到答案；
    /// - 若 `nums[i] != 0`，`cur` 清零。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn zero_filled_subarray(nums: Vec<i32>) -> i64 {
        let mut count = 0i64;
        let mut run = 0i64;

        for x in nums {
            run = if x == 0 { run + 1 } else { 0 };
            count += run;
        }

        count
    }
}
```
