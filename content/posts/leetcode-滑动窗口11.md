---
title: "leetcode-滑动窗口11"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最长和谐子序列

和谐数组是指一个数组里元素的最大值和最小值之间的差别 正好是 1 。

给你一个整数数组 nums ，请你在所有可能的 子序列 中找到最长的和谐子序列的长度。

数组的 子序列 是一个由数组派生出来的序列，它可以通过删除一些元素或不删除元素、且不改变其余元素的顺序而得到。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_lhs(nums: Vec<i32>) -> i32 {
        let mut freq = HashMap::new();

        // 统计每个数字出现次数
        for &num in &nums {
            *freq.entry(num).or_insert(0) += 1;
        }

        let mut max_len = 0;

        // 检查每个数字 x 和 x+1 的组合
        for (&x, &count) in &freq {
            if let Some(&next_count) = freq.get(&(x + 1)) {
                max_len = max_len.max(count + next_count);
            }
        }

        max_len
    }
}
```
