---
title: "leetcode-滑动窗口63"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计好子数组的数目

给你一个整数数组 nums 和一个整数 k ，请你返回 nums 中 好 子数组的数目。

一个子数组 arr 如果有 至少 k 对下标 (i, j) 满足 i < j 且 arr[i] == arr[j] ，那么称它是一个 好 子数组。

子数组 是原数组中一段连续 非空 的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_good(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;
        let mut total_pairs = 0;      // 当前窗口中相等元素对的数量
        let mut left = 0;
        let mut freq = HashMap::with_capacity(n);
        let mut result = 0;

        // 滑动窗口：对每个右端点，找到最小的左端点使得窗口成为好子数组
        for right in 0..n {
            let val = nums[right];
            // 新增元素带来的相等对数量 = 当前窗口中该元素的出现次数
            let count = freq.get(&val).copied().unwrap_or(0);
            total_pairs += count;
            freq.insert(val, count + 1);

            // 如果当前窗口是好的，左移左指针以找到最短的好子数组
            while total_pairs >= k {
                // 以当前left为起点，right及之后所有位置为终点都满足条件
                result += (n - right) as i64;

                // 移除左端元素
                let left_val = nums[left];
                let left_count = freq.get(&left_val).copied().unwrap_or(0);
                // 移除该元素后，减少的相等对数量 = 窗口中剩余该元素的数量
                total_pairs -= left_count - 1;
                freq.insert(left_val, left_count - 1);
                left += 1;
            }
        }

        result
    }
}
```
