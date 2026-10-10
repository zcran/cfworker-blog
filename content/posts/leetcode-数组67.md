---
title: "leetcode-数组67"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 唯一中间元素

给你一个长度为奇数 n 的整数数组 nums 。

如果 nums 的下标中间元素在数组中 恰好 出现一次，返回 true 。否则返回 false 。


```
impl Solution {
    pub fn is_middle_element_unique(nums: Vec<i32>) -> bool {
        // 中间元素在数组中恰好出现一次
        let mid = nums[nums.len() / 2];
        nums.iter().filter(|&&x| x == mid).count() == 1
    }
}
```
