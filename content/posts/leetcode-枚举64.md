---
title: "leetcode-枚举64"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计 1 显著的字符串的数量

给你一个二进制字符串 s。

请你统计并返回其中 1 显著 的 子字符串 的数量。

如果字符串中 1 的数量 大于或等于 0 的数量的 平方，则认为该字符串是一个 1 显著 的字符串 。


```
impl Solution {
    pub fn number_of_substrings(s: String) -> i32 {
        let chars = s.as_bytes();
        let n = chars.len();

        // pre[i] 表示位置 i 之前（包含 i-1）最后一个 0 的位置
        // 用于快速跳转到前一个 0
        let mut prev_zero = vec![-1; n + 1];
        for i in 0..n {
            prev_zero[i + 1] = if i == 0 || chars[i - 1] == b'0' {
                i as i32
            } else {
                prev_zero[i]
            };
        }

        let mut ans = 0;

        // 枚举子串的右端点 i（1-based）
        for right in 1..=n {
            let mut zeros = if chars[right - 1] == b'0' { 1 } else { 0 };
            let mut pos = right as i32; // 当前考虑的起始位置

            // 跳转到前一个 0，枚举 0 的数量
            while pos > 0 && (zeros * zeros) as usize <= n {
                // 当前区间 [pos, right] 中有 zeros 个 0
                let ones = (right as i32 - prev_zero[pos as usize]) - zeros;

                // 需要满足：ones >= zeros^2
                // 可以向左扩展起始位置，直到前一个 0 之前
                if zeros * zeros <= ones {
                    // 可扩展的数量 = min(到前一个0的距离, ones - zeros^2 + 1)
                    let extend = (pos - prev_zero[pos as usize]).min(ones - zeros * zeros + 1);
                    ans += extend;
                }

                // 跳到前一个 0 的位置，0 的数量增加 1
                pos = prev_zero[pos as usize];
                zeros += 1;
            }
        }

        ans
    }
}
```
