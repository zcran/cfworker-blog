---
title: "leetcode-前缀和81"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计美丽子数组数目

给你一个下标从 0 开始的整数数组nums 。每次操作中，你可以：

· 选择两个满足 0 <= i, j < nums.length 的不同下标 i 和 j 。
· 选择一个非负整数 k ，满足 nums[i] 和 nums[j] 在二进制下的第 k 位（下标编号从 0 开始）是 1 。
· 将 nums[i] 和 nums[j] 都减去 2k 。

如果一个子数组内执行上述操作若干次（包括 0 次）后，该子数组可以变成一个全为 0 的数组，那么我们称它是一个 美丽 的子数组。

请你返回数组 nums 中 美丽子数组 的数目。

子数组是一个数组中一段连续 非空 的元素序列。

注意：所有元素最初都是 0 的子数组被认为是美丽的，因为不需要进行任何操作。


```
use std::collections::HashMap;

impl Solution {
    pub fn beautiful_subarrays(nums: Vec<i32>) -> i64 {
        // 存储每个前缀异或值出现的次数
        let mut prefix_count = HashMap::new();
        prefix_count.insert(0, 1);

        let mut xor = 0;
        let mut ans = 0;

        for num in nums {
            xor ^= num;
            // 当前前缀异或值之前出现过的次数，即为以当前元素结尾的美丽子数组数
            ans += prefix_count.get(&xor).copied().unwrap_or(0);
            *prefix_count.entry(xor).or_insert(0) += 1;
        }

        ans
    }
}
```
