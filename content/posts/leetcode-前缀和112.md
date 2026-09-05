---
title: "leetcode-前缀和112"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计元素和差值为偶数的分区方案

给你一个长度为 n 的整数数组 nums 。

分区 是指将数组按照下标 i （0 <= i < n - 1）划分成两个 非空 子数组，其中：

左子数组包含区间 [0, i] 内的所有下标。

右子数组包含区间 [i + 1, n - 1] 内的所有下标。

对左子数组和右子数组先求元素 和 再做 差 ，统计并返回差值为 偶数 的 分区 方案数。


```
impl Solution {
    pub fn count_partitions(nums: Vec<i32>) -> i32 {
        // 左子数组和与右子数组和的差值为：
        // left_sum - right_sum = left_sum - (total_sum - left_sum) = 2 * left_sum - total_sum
        // 要使其为偶数，只需 total_sum 为偶数（因为 2*left_sum 一定是偶数）
        if nums.iter().sum::<i32>() % 2 == 0 {
            nums.len() as i32 - 1 // 分区方案数为 n-1
        } else {
            0
        }
    }
}
```
