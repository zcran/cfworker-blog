---
title: "leetcode-数组20"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 是否所有 1 都至少相隔 k 个元素

给你一个由若干 0 和 1 组成的数组 nums 以及整数 k。如果所有 1 都至少相隔 k 个元素，则返回 true ；否则，返回 false 。




```
impl Solution {
    /// 判断数组中所有 1 之间是否至少相隔 k 个元素。
    ///
    /// 遍历时记录上一个 1 的下标 `prev`。遇到新的 1 时，
    /// 若与上一个 1 之间间隔的元素数 `< k`，则返回 false。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn k_length_apart(nums: Vec<i32>, k: i32) -> bool {
        let mut prev = -1;

        for (i, &num) in nums.iter().enumerate() {
            if num == 1 {
                if prev != -1 && (i as i32 - prev - 1) < k {
                    return false;
                }
                prev = i as i32;
            }
        }

        true
    }
}
```
