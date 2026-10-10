---
title: "leetcode-数组18"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 二进制字符串前缀一致的次数

给你一个长度为 n 、下标从 1 开始的二进制字符串，所有位最开始都是 0 。我们会按步翻转该二进制字符串的所有位（即，将 0 变为 1）。

给你一个下标从 1 开始的整数数组 flips ，其中 flips[i] 表示对应下标 i 的位将会在第 i 步翻转。

二进制字符串 前缀一致 需满足：在第 i 步之后，在 闭 区间 [1, i] 内的所有位都是 1 ，而其他位都是 0 。

返回二进制字符串在翻转过程中 前缀一致 的次数。


```
impl Solution {
    /// 统计翻转过程中「前缀一致」的次数。
    ///
    /// 第 i 步（0-based）后，闭区间 [1, i+1] 内所有位为 1，
    /// 等价于：前 i+1 步翻转的下标最大值恰好为 i+1。
    /// 遍历时维护已翻转下标的最大值 `max_flip`，
    /// 若 `max_flip == i + 1`，说明前 i+1 位恰好被全部翻过。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn num_times_all_blue(flips: Vec<i32>) -> i32 {
        let mut count = 0;
        let mut max_flip = 0;

        for (i, &f) in flips.iter().enumerate() {
            max_flip = max_flip.max(f);
            if max_flip == i as i32 + 1 {
                count += 1;
            }
        }

        count
    }
}
```
