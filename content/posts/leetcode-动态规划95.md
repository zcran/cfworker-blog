---
title: "leetcode-动态规划95"
date: 2026-10-05T20:40:00+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 不同的好子序列数目

给你一个二进制字符串 binary 。 binary 的一个 子序列 如果是 非空 的且没有 前导 0 （除非数字是 "0" 本身），那么它就是一个 好 的子序列。

请你找到 binary 不同好子序列 的数目。

比方说，如果 binary = "001" ，那么所有 好 子序列为 ["0", "0", "1"] ，所以 不同 的好子序列为 "0" 和 "1" 。 注意，子序列 "00" ，"01" 和 "001" 不是好的，因为它们有前导 0 。

请你返回 binary 中 不同好子序列 的数目。由于答案可能很大，请将它对 109 + 7 取余 后返回。

一个 子序列 指的是从原数组中删除若干个（可以一个也不删除）元素后，不改变剩余元素顺序得到的序列。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn number_of_unique_good_subsequences(binary: String) -> i32 {
        // even: 以 '0' 结尾的不同好子序列数
        // odd:  以 '1' 结尾的不同好子序列数
        let mut even = 0i64;
        let mut odd = 0i64;
        let mut has_zero = false;

        for b in binary.bytes() {
            if b == b'0' {
                // '0' 接在任意以 '1' 结尾的好子序列后，形成新的以 '0' 结尾的子序列
                even = (even + odd) % MOD;
                has_zero = true;
            } else {
                // '1' 可以单独成串（+1），或接在任意好子序列后
                odd = (even + odd + 1) % MOD;
            }
        }

        // 加上单独的 "0"（若存在）
        let ans = (even + odd + has_zero as i64) % MOD;
        ans as i32
    }
}
```
