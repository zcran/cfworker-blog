---
title: "leetcode-哈希表55"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 和相等的子数组

给你一个下标从 0 开始的整数数组 nums ，判断是否存在 两个 长度为 2 的子数组且它们的 和 相等。注意，这两个子数组起始位置的下标必须 不相同 。

如果这样的子数组存在，请返回 true，否则返回 false 。

子数组 是一个数组中一段连续非空的元素组成的序列。


```
impl Solution {
    pub fn find_subarrays(nums: Vec<i32>) -> bool {
        use std::collections::HashSet;

        // 记录已出现过的相邻两元素之和
        let mut seen = HashSet::with_capacity(nums.len());
        for w in nums.windows(2) {
            // insert 返回 false 表示该和之前已出现过
            if !seen.insert(w[0] + w[1]) {
                return true;
            }
        }
        false
    }
}
```
