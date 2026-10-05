---
title: "leetcode-位运算64"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 求出出现两次数字的 XOR 值

给你一个数组 nums ，数组中的数字 要么 出现一次，要么 出现两次。

请你返回数组中所有出现两次数字的按位 XOR 值，如果没有数字出现过两次，返回 0 。


```
impl Solution {
    pub fn duplicate_numbers_xor(nums: Vec<i32>) -> i32 {
        // 题目保证每个数字最多出现两次，因此用一个 HashSet 记录见过的数字
        let mut seen = std::collections::HashSet::new();
        let mut res = 0;

        for num in nums {
            // insert 返回 true 表示首次插入；返回 false 表示已存在，即第二次出现
            if !seen.insert(num) {
                res ^= num;
            }
        }

        res
    }
}
```
