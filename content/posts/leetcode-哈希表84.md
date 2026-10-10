---
title: "leetcode-哈希表84"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 得到互不相同元素的最少操作次数

给你一个整数数组 nums。

在一次操作中，你需要移除当前数组的 前三个元素。如果剩余元素少于三个，则移除 所有 剩余元素。

重复此操作，直到数组为空或不包含任何重复元素为止。

返回一个整数，表示所需的操作次数。


```
use std::collections::HashSet;

impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        // 从右往左找到第一个重复元素，它及其左侧元素都必须被移除
        let mut seen = HashSet::new();
        let mut suffix_len = 0;

        for &v in nums.iter().rev() {
            if !seen.insert(v) {
                break;
            }
            suffix_len += 1;
        }

        // 需要移除的元素个数 = 总长度 - 可保留的后缀长度
        let to_remove = nums.len() - suffix_len;
        ((to_remove + 2) / 3) as i32
    }
}
```
