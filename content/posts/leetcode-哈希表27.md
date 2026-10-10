---
title: "leetcode-哈希表27"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 在长度 2N 的数组中找出重复 N 次的元素

给你一个整数数组 nums ，该数组具有以下属性：

nums.length == 2 * n.
nums 包含 n + 1 个 不同的 元素，其中 n 个值在数组中出现 恰好一次。
nums 中恰有一个元素重复 n 次

找出并返回重复了 n 次的那个元素。


```
use std::collections::HashSet;

impl Solution {
    pub fn repeated_n_times(nums: Vec<i32>) -> i32 {
        // 重复 n 次的元素是唯一出现次数大于 1 的元素，用集合找首个重复即可。
        let mut seen = HashSet::new();
        for &x in &nums {
            if !seen.insert(x) {
                return x;
            }
        }
        unreachable!()
    }
}
```
