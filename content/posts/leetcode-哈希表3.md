---
title: "leetcode-哈希表3"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 缺失的第一个正数

给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。

请你实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案。


```
impl Solution {
    pub fn first_missing_positive(mut nums: Vec<i32>) -> i32 {
        // 原地哈希：让值 v ∈ [1, n] 落到下标 v-1 的位置上。
        // 交换后 nums[i] 变成新值，需继续处理，因此用 while 而非 if。
        let n = nums.len();
        for i in 0..n {
            while nums[i] >= 1
                && nums[i] <= n as i32
                && nums[(nums[i] - 1) as usize] != nums[i]
            {
                let j = (nums[i] - 1) as usize;
                nums.swap(i, j);
            }
        }

        // 首个 nums[i] != i+1 的位置，缺失的正整数即为 i+1。
        for i in 0..n {
            if nums[i] != (i + 1) as i32 {
                return (i + 1) as i32;
            }
        }

        // 1..=n 全部就位，缺失的是 n+1。
        (n + 1) as i32
    }
}
```
