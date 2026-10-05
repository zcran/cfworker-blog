---
title: "leetcode-位运算29"
date: 2026-09-24T10:33:35+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 找出最长的超赞子字符串

给你一个字符串 s 。请返回 s 中最长的 超赞子字符串 的长度。

「超赞子字符串」需满足满足下述两个条件：

该字符串是 s 的一个非空子字符串
进行任意次数的字符交换后，该字符串可以变成一个回文字符串


```
use std::collections::HashMap;

impl Solution {
    /// 返回 s 中最长「超赞子字符串」的长度。
    ///
    /// 一个字符串能通过字符交换变成回文，等价于其中最多只有一种字符出现奇数次。
    /// 用 10 位掩码表示 0..=9 各字符出现次数的奇偶性，
    /// 则任一子串的奇偶掩码 = 前缀掩码的异或。
    ///
    /// 对每个位置 j 的前缀掩码 mask：
    /// - 若之前出现过相同的 mask，则两者之间的子串所有位均为偶数，长度 = j - 首次出现位置；
    /// - 若之前出现过 mask 恰好相差一位的掩码，则子串恰有一位为奇数，同样合法。
    pub fn longest_awesome(s: String) -> i32 {
        // 记录每个前缀掩码首次出现的下标；下标 -1 表示空前缀
        let mut first_seen: HashMap<i32, i32> = HashMap::new();
        first_seen.insert(0, -1);

        let mut ans = 0;
        let mut mask = 0;

        for (j, byte) in s.bytes().enumerate() {
            let j = j as i32;
            // 翻转当前数字对应的奇偶位
            mask ^= 1 << (byte - b'0');

            // 情况一：整个子串所有数字均为偶数次
            if let Some(&prev) = first_seen.get(&mask) {
                ans = ans.max(j - prev);
            } else {
                first_seen.insert(mask, j);
            }

            // 情况二：子串中恰有一位为奇数次
            for bit in 0..10 {
                if let Some(&prev) = first_seen.get(&(mask ^ (1 << bit))) {
                    ans = ans.max(j - prev);
                }
            }
        }

        ans
    }
}
```
