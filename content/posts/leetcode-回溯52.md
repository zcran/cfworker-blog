---
title: "leetcode-回溯52"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 找出不同的二进制字符串

给你一个字符串数组 nums ，该数组由 n 个 互不相同 的二进制字符串组成，且每个字符串长度都是 n 。请你找出并返回一个长度为 n 且 没有出现 在 nums 中的二进制字符串。如果存在多种答案，只需返回 任意一个 即可。


```
use std::collections::HashSet;

impl Solution {
    pub fn find_different_binary_string(nums: Vec<String>) -> String {
        let n = nums.len();

        // 将 nums 中所有二进制字符串转换为整数并存入集合
        let mut seen = HashSet::with_capacity(n);
        for s in &nums {
            seen.insert(i32::from_str_radix(s, 2).unwrap());
        }

        // 从 0 开始找第一个不在集合中的整数
        let mut num = 0;
        while seen.contains(&num) {
            num += 1;
        }

        // 格式化为长度为 n 的二进制字符串（不足补前导零）
        format!("{:0>width$b}", num, width = n)
    }
}
```
