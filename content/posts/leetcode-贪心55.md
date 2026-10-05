---
title: "leetcode-贪心55"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 三次操作后最大值与最小值的最小差

给你一个数组 nums 。

每次操作你可以选择 nums 中的任意一个元素并将它改成 任意值 。

在 执行最多三次操作后 ，返回 nums 中最大值与最小值的最小差值。


```
impl Solution {
    pub fn min_difference(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n <= 4 {
            return 0;
        }

        // 排序后只需考虑保留首尾若干元素：
        // 最多改 3 个，最小值必在原数组最小的 4 个中，最大值必在最大的 4 个中
        let mut nums = nums;
        nums.sort_unstable();

        // 枚举删掉左侧 i 个、右侧 (3 - i) 个，剩余首尾差的最小值
        let mut ans = i32::MAX;
        for i in 0..=3 {
            ans = ans.min(nums[n - 1 - (3 - i)] - nums[i]);
        }
        ans
    }
}
```
