---
title: "leetcode-数学31"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 统计同质子字符串的数目

给你一个字符串 s ，返回 s 中 同质子字符串 的数目。由于答案可能很大，只需返回对 109 + 7 取余 后的结果。

同质字符串 的定义为：如果一个字符串中的所有字符都相同，那么该字符串就是同质字符串。

子字符串 是字符串中的一个连续字符序列。


```
impl Solution {
    pub fn count_homogenous(s: String) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 逐段统计连续相同字符的长度 len，
        // 每段贡献 len * (len + 1) / 2 个同质子串。
        // 用 i64 运算避免乘法溢出。
        let bytes = s.as_bytes();
        let mut total: i64 = 0;
        let mut run: i64 = 1;

        for i in 1..bytes.len() {
            if bytes[i] == bytes[i - 1] {
                run += 1;
            } else {
                total = (total + run * (run + 1) / 2) % MOD;
                run = 1;
            }
        }

        // 处理最后一段
        ((total + run * (run + 1) / 2) % MOD) as i32
    }
}
```
