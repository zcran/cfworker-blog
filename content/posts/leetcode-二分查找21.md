---
title: "leetcode-二分查找21"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 寻找比目标字母大的最小字母

给你一个字符数组 letters，该数组按 非递减顺序 排序，以及一个字符 target。letters 里至少有两个不同的字符。

返回 letters 中大于 target 的最小的字符。如果不存在这样的字符，则返回 letters 的第一个字符。


```
impl Solution {
    /// 返回 letters 中大于 target 的最小字符；若不存在则返回首字符。
    /// letters 已按非递减顺序排序，且至少包含两个不同字符。
    pub fn next_greatest_letter(letters: Vec<char>, target: char) -> char {
        // 二分查找第一个严格大于 target 的位置
        let idx = letters.partition_point(|&c| c <= target);
        // idx == len 表示所有字符都 <= target，按题意返回首字符
        letters[idx % letters.len()]
    }
}
```
