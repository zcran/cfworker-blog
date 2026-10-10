---
title: "leetcode-数学46"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 数组元素和与数字和的绝对差

给你一个正整数数组 nums 。

元素和 是 nums 中的所有元素相加求和。
数字和 是 nums 中每一个元素的每一数位（重复数位需多次求和）相加求和。

返回 元素和 与 数字和 的绝对差。

注意：两个整数 x 和 y 的绝对差定义为 |x - y| 。




```
impl Solution {
    pub fn difference_of_sum(nums: Vec<i32>) -> i32 {
        // 元素和：直接累加；数字和：对每个元素逐位取余累加。
        let mut element_sum = 0;
        let mut digit_sum = 0;

        for mut num in nums {
            element_sum += num;
            while num > 0 {
                digit_sum += num % 10;
                num /= 10;
            }
        }

        // 元素和 >= 数字和，绝对值即两者之差
        element_sum - digit_sum
    }
}
```
