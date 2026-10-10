---
title: "leetcode-数组56"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 好数字之和

给定一个整数数组 nums 和一个整数 k，如果元素 nums[i] 严格 大于下标 i - k 和 i + k 处的元素（如果这些元素存在），则该元素 nums[i] 被认为是 好 的。如果这两个下标都不存在，那么 nums[i] 仍然被认为是 好 的。

返回数组中所有 好 元素的 和。


```
impl Solution {
    pub fn sum_of_good_numbers(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        // 好元素：严格大于相距 k 的左右邻居（越界则视为不存在）
        nums.iter()
            .enumerate()
            .filter(|&(i, &x)| {
                (i < k || x > nums[i - k]) && (i + k >= nums.len() || x > nums[i + k])
            })
            .map(|(_, &x)| x)
            .sum()
    }
}
```
