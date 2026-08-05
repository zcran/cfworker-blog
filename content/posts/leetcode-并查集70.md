---
title: "leetcode-并查集70"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 最长连续序列

给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。


```
use std::collections::HashSet;

impl Solution {
    /// 寻找最长连续序列长度（哈希集合 + 贪心起点）
    pub fn longest_consecutive(nums: Vec<i32>) -> i32 {
        let set: HashSet<_> = nums.into_iter().collect();
        let mut max_len = 0;

        for &x in &set {
            // 只有当 x 是连续序列的起点时才计数
            if !set.contains(&(x - 1)) {
                let mut cur = x;
                let mut len = 1;
                while set.contains(&(cur + 1)) {
                    cur += 1;
                    len += 1;
                }
                max_len = max_len.max(len);
            }
        }

        max_len
    }
}
```
