---
title: "leetcode-数学67"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 替换为数位和以后的最小元素

给你一个整数数组 nums 。

请你将 nums 中每一个元素都替换为它的各个数位之 和 。

请你返回替换所有元素以后 nums 中的 最小 元素。


```
impl Solution {
    pub fn min_element(nums: Vec<i32>) -> i32 {
        // 对每个元素求各数位之和，返回其中的最小值。
        // 题目约束 nums[i] <= 10^4，数位和最大为 36（9999），故用 37 作初值。
        nums.into_iter()
            .map(|mut n| {
                let mut digit_sum = 0;
                while n > 0 {
                    digit_sum += n % 10;
                    n /= 10;
                }
                digit_sum
            })
            .min()
            .unwrap()
    }
}
```
