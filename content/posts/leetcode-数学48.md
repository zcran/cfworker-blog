---
title: "leetcode-数学48"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 找出字符串的可整除数组

给你一个下标从 0 开始的字符串 word ，长度为 n ，由从 0 到 9 的数字组成。另给你一个正整数 m 。

word 的 可整除数组 div  是一个长度为 n 的整数数组，并满足：

如果 word[0,...,i] 所表示的 数值 能被 m 整除，div[i] = 1
否则，div[i] = 0

返回 word 的可整除数组。


```
impl Solution {
    pub fn divisibility_array(word: String, m: i32) -> Vec<i32> {
        // 逐位构造前缀数值，但只保留其对 m 的余数，避免大数溢出。
        // 余数为 0 则说明该前缀能被 m 整除。
        let m = m as i64;
        let mut rem = 0i64;
        let mut res = Vec::with_capacity(word.len());

        for b in word.bytes() {
            rem = (rem * 10 + (b - b'0') as i64) % m;
            res.push(if rem == 0 { 1 } else { 0 });
        }

        res
    }
}
```
