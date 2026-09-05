---
title: "leetcode-前缀和21"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 逐步求和得到正数的最小值

给你一个整数数组 nums 。你可以选定任意的 正数 startValue 作为初始值。

你需要从左到右遍历 nums 数组，并将 startValue 依次累加上 nums 数组中的值。

请你在确保累加和始终大于等于 1 的前提下，选出一个最小的 正数 作为 startValue 。


```
impl Solution {
    pub fn min_start_value(nums: Vec<i32>) -> i32 {
        let mut min_prefix = 0;
        let mut prefix = 0;

        // 计算所有前缀和，并记录最小值
        for &num in &nums {
            prefix += num;
            if prefix < min_prefix {
                min_prefix = prefix;
            }
        }

        // 需要使最小前缀和 >= 1，即 startValue + min_prefix >= 1
        1 - min_prefix
    }
}
```
