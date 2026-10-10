---
title: "leetcode-字符串82"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 使二进制字符串连贯的最少翻转次数

给你一个二进制字符串 s。

如果一个字符串 不 包含 "011" 或 "110" 作为 子序列，则认为该字符串是 连贯的 。

在一次操作中，你可以 翻转  s 中的任意字符（'0' 变为 '1'，或 '1' 变为 '0'）。

返回一个整数，表示使 s 连贯所需的 最少 操作次数。


```
impl Solution {
    pub fn min_flips(s: String) -> i32 {
        let b = s.as_bytes();
        let n = b.len();

        // 连贯字符串只能是：全 0、全 1、或形如 1?0+1? 的形式
        // 即除两端各最多保留一个 '1' 外，其余 '1' 都要翻成 '0'
        let ones = b.iter().filter(|&&c| c == b'1').count() as i32;
        let zeros = n as i32 - ones;

        // 首尾都是 '1' 时可以两端各保留一个，扣除 2；否则扣除 1
        let keep = if b[0] == b'1' && b[n - 1] == b'1' { 2 } else { 1 };

        // 方案一：转成 1?0+1? 形式，需翻转的 '1' 个数
        // 方案二：全部转成 '1'，需翻转的 '0' 个数
        (ones - keep).max(0).min(zeros)
    }
}
```
