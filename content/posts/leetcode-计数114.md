---
title: "leetcode-计数114"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 找到频率最高的元音和辅音

给你一个由小写英文字母（'a' 到 'z'）组成的字符串 s。你的任务是找出出现频率 最高 的元音（'a'、'e'、'i'、'o'、'u' 中的一个）和出现频率最高的辅音（除元音以外的所有字母），并返回这两个频数之和。

注意：如果有多个元音或辅音具有相同的最高频率，可以任选其中一个。如果字符串中没有元音或没有辅音，则其频率视为 0。

一个字母 x 的 频率 是它在字符串中出现的次数。


```
impl Solution {
    /// 返回字符串中出现频率最高的元音和辅音的频数之和。
    /// 若字符串中不含元音或辅音，对应频率视为 0。
    pub fn max_freq_sum(s: String) -> i32 {
        // 元音标记表：a(0), e(4), i(8), o(14), u(20) 为 true
        const VOWEL: [bool; 26] = {
            let mut v = [false; 26];
            v[0] = true;   // a
            v[4] = true;   // e
            v[8] = true;   // i
            v[14] = true;  // o
            v[20] = true;  // u
            v
        };

        let mut freq = [0u32; 26];
        // 直接遍历字节，避免 UTF-8 解码（题目保证仅含小写字母）
        for &b in s.as_bytes() {
            freq[(b - b'a') as usize] += 1;
        }

        let (mut max_vowel, mut max_consonant) = (0u32, 0u32);
        for (i, &f) in freq.iter().enumerate() {
            if VOWEL[i] {
                if f > max_vowel { max_vowel = f; }
            } else {
                if f > max_consonant { max_consonant = f; }
            }
        }

        (max_vowel + max_consonant) as i32
    }
}
```
