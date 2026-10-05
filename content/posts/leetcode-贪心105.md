---
title: "leetcode-贪心105"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 子字符串的最优划分

给你一个字符串 s ，请你将该字符串划分成一个或多个 子字符串 ，并满足每个子字符串中的字符都是 唯一 的。也就是说，在单个子字符串中，字母的出现次数都不超过 一次 。

满足题目要求的情况下，返回 最少 需要划分多少个子字符串。

注意，划分后，原字符串中的每个字符都应该恰好属于一个子字符串。


```
impl Solution {
    pub fn partition_string(s: String) -> i32 {
        // 用一个 32 位整数作位图记录当前子串中已出现的字符。
        // 遇到重复字符就另起一段（res + 1），并重置位图。
        // 最终段数为分割次数 + 1。
        s.bytes().fold((0u32, 1), |(seen, parts), b| {
            let bit = 1u32 << (b - b'a');
            if seen & bit != 0 {
                (bit, parts + 1)
            } else {
                (seen | bit, parts)
            }
        }).1
    }
}
```
