---
title: "leetcode-哈希表61"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 最小化字符串长度

给你一个下标从 0 开始的字符串 s ，重复执行下述操作 任意 次：

在字符串中选出一个下标 i ，并使 c 为字符串下标 i 处的字符。并在 i 左侧（如果有）和 右侧（如果有）各 删除 一个距离 i 最近 的字符 c 。

请你通过执行上述操作任意次，使 s 的长度 最小化 。

返回一个表示 最小化 字符串的长度的整数。


```
impl Solution {
    pub fn minimized_string_length(s: String) -> i32 {
        // 每次操作可删除某个字符左右两侧各一个相同字符，
        // 最终每种出现过的字符最多只能保留一个，
        // 因此答案就是不同字符的种类数。
        let mut mask = 0u32;
        for b in s.bytes() {
            mask |= 1 << (b - b'a');
        }
        mask.count_ones() as i32
    }
}
```
