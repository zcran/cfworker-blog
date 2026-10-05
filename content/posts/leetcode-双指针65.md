---
title: "leetcode-双指针65"
date: 2026-09-05T09:53:04+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 检查整数及其两倍数是否存在

给你一个整数数组 arr，请你检查是否存在两个整数 N 和 M，满足 N 是 M 的两倍（即，N = 2 * M）。

更正式地，检查是否存在两个下标 i 和 j 满足：

i != j
0 <= i, j < arr.length
arr[i] == 2 * arr[j]


```
impl Solution {
    pub fn check_if_exist(arr: Vec<i32>) -> bool {
        use std::collections::HashSet;

        let mut seen = HashSet::with_capacity(arr.len());

        for &num in &arr {
            // 检查当前数字是否满足条件：
            // 1. 存在之前见过的数字 num * 2（当前是较大数）
            // 2. 存在之前见过的数字 num / 2（当前是较小数，且能被2整除）
            if seen.contains(&(num * 2)) || (num % 2 == 0 && seen.contains(&(num / 2))) {
                return true;
            }
            seen.insert(num);
        }

        false
    }
}
```
