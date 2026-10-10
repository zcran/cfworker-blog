---
title: "leetcode-动态规划147"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计可以被最后一个数位整除的子字符串数目

给你一个只包含数字的字符串 s 。

请你返回 s 的最后一位 不是 0 的子字符串中，可以被子字符串最后一位整除的数目。

子字符串 是一个字符串里面一段连续 非空 的字符序列。

注意：子字符串可以有前导 0 。


```
impl Solution {
    /// 逐位 DP：对每个模数 d（1..=9）维护以当前字符结尾的子串值对 d 取模的余数计数。
    /// 新数字 dig 到来时，余数按 (r * 10 + dig) % d 迁移，并单独计入长度为 1 的子串。
    /// 答案为每个非零 dig 处，模 dig 余 0 的子串数之和。
    /// 时间复杂度 O(90n)，空间复杂度 O(1)。
    pub fn count_substrings(s: String) -> i64 {
        // cnt[d][r]：以当前字符结尾、模 d 余 r 的子串个数
        let mut cnt = [[0i64; 10]; 10];
        let mut ans = 0i64;

        for &ch in s.as_bytes() {
            let dig = (ch - b'0') as usize;

            // 迁移：所有旧子串末尾追加 dig，并计入新的长度为 1 的子串
            let mut next = [[0i64; 10]; 10];
            for d in 1..=9 {
                for r in 0..d {
                    if cnt[d][r] > 0 {
                        next[d][(r * 10 + dig) % d] += cnt[d][r];
                    }
                }
                next[d][dig % d] += 1;
            }
            cnt = next;

            // 只统计最后一位非 0 的子串
            if dig != 0 {
                ans += cnt[dig][0];
            }
        }

        ans
    }
}
```
