---
title: "leetcode-排序78"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 二进制反射排序

给你一个整数数组 nums。

二进制反射 是对一个 正整数 的二进制表示按顺序反转（忽略前导零）后，将反转得到的二进制数转为十进制的结果。

请按每个元素的二进制反射值的 升序 对数组进行排序。如果两个不同的数字具有相同的二进制反射值，则 较小 的原始数字应排在前面。

返回排序后的数组。




```
impl Solution {
    pub fn sort_by_reflection(mut nums: Vec<i32>) -> Vec<i32> {
        // 计算二进制反射值：反转二进制位后，丢弃前导零（即右移到最低有效位）
        fn reflection(x: i32) -> i32 {
            (x.reverse_bits() as u32 >> x.leading_zeros()) as i32
        }

        // 先按反射值升序，反射值相同则按原始数值升序
        nums.sort_unstable_by_key(|&v| (reflection(v), v));
        nums
    }
}
```
