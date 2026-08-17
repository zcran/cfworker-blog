---
title: "leetcode-计数77"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 摧毁一系列目标

给你一个下标从 0 开始的数组 nums ，它包含若干正整数，表示数轴上你需要摧毁的目标所在的位置。同时给你一个整数 space 。

你有一台机器可以摧毁目标。给机器 输入 nums[i] ，这台机器会摧毁所有位置在 nums[i] + c * space 的目标，其中 c 是任意非负整数。你想摧毁 nums 中 尽可能多 的目标。

请你返回在摧毁数目最多的前提下，nums[i] 的 最小值 。


```
use std::collections::HashMap;

impl Solution {
    pub fn destroy_targets(nums: Vec<i32>, space: i32) -> i32 {
        // 统计每个余数对应的目标数量
        // 同一余数的目标可以被同一个输入摧毁（相差 space 的整数倍）
        let mut freq = HashMap::with_capacity(nums.len());
        for &num in &nums {
            let key = num % space;
            *freq.entry(key).or_insert(0) += 1;
        }

        // 找出最多目标所在的余数，并返回该余数中最小的 nums 值
        let mut max_count = 0;
        let mut min_num = i32::MAX;

        for &num in &nums {
            let count = freq[&(num % space)];
            if count > max_count || (count == max_count && num < min_num) {
                max_count = count;
                min_num = num;
            }
        }

        min_num
    }
}
```
