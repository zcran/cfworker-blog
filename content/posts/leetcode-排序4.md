---
title: "leetcode-排序4"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 存在重复元素

给你一个整数数组 nums 。如果任一值在数组中出现 至少两次 ，返回 true ；
如果数组中每个元素互不相同，返回 false 。


```
use std::collections::HashSet;

impl Solution {
    /// 判断数组中是否存在重复元素
    ///
    /// 思路：边遍历边插入 HashSet，一旦插入失败即说明出现重复，
    /// 可提前返回，避免像「全部收集后比较长度」那样总是遍历完整数组。
    pub fn contains_duplicate(nums: Vec<i32>) -> bool {
        let mut seen = HashSet::with_capacity(nums.len());
        nums.iter().any(|&x| !seen.insert(x))
    }
}
```
