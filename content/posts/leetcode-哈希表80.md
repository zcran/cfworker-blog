---
title: "leetcode-哈希表80"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 缺失的最小倍数

给你一个整数数组 nums 和一个整数 k，请返回从 nums 中缺失的、最小的正整数 k 的倍数。

倍数 指能被 k 整除的任意正整数。




```
use std::collections::HashSet;

impl Solution {
    pub fn missing_multiple(nums: Vec<i32>, k: i32) -> i32 {
        // 用集合快速判断某个值是否已出现
        let present: HashSet<i32> = nums.into_iter().collect();

        // 从最小的正倍数 k 开始，逐个检查是否缺失
        let mut ans = k;
        while present.contains(&ans) {
            ans += k;
        }
        ans
    }
}
```
