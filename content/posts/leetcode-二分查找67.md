---
title: "leetcode-二分查找67"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 将三个组排序

给你一个整数数组 nums 。nums 的每个元素是 1，2 或 3。在每次操作中，你可以删除 nums 中的一个元素。返回使 nums 成为 非递减 顺序所需操作数的 最小值。


```
impl Solution {
    pub fn minimum_operations(nums: Vec<i32>) -> i32 {
        // 等价于求最长非递减子序列长度，答案为 n - 该长度
        // 由于元素只有 1、2、3，可以用三个变量分别维护以 1/2/3 结尾的最长非递减子序列长度
        let mut dp = [0_i32; 3];
        for &x in &nums {
            let idx = (x - 1) as usize;
            // 以 x 结尾的最长非递减子序列：可以由 <= x 的任意结尾转移而来
            let best = dp[..=idx].iter().copied().max().unwrap();
            dp[idx] = dp[idx].max(best + 1);
        }

        let lis = *dp.iter().max().unwrap();
        nums.len() as i32 - lis
    }
}
```
