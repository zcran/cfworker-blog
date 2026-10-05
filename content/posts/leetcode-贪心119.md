---
title: "leetcode-贪心119"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 字典序最小的美丽字符串

如果一个字符串满足以下条件，则称其为 美丽字符串 ：

它由英语小写字母表的前 k 个字母组成。
它不包含任何长度为 2 或更长的回文子字符串。

给你一个长度为 n 的美丽字符串 s 和一个正整数 k 。

请你找出并返回一个长度为 n 的美丽字符串，该字符串还满足：在字典序大于 s 的所有美丽字符串中字典序最小。如果不存在这样的字符串，则返回一个空字符串。

对于长度相同的两个字符串 a 和 b ，如果字符串 a 在与字符串 b 不同的第一个位置上的字符字典序更大，则字符串 a 的字典序大于字符串 b 。

例如，"abcd" 的字典序比 "abcc" 更大，因为在不同的第一个位置（第四个字符）上 d 的字典序大于 c 。


```
impl Solution {
    pub fn smallest_beautiful_string(s: String, k: i32) -> String {
        // 美丽字符串只需避免长度 ≥2 的回文：
        // 即相邻两字符不同，且隔一个的两字符不同。
        let k = k as u8;
        let mut bytes = s.into_bytes();
        let n = bytes.len();

        // 从右往左尝试增大某一位，再将其后所有位重置为最小可行字符
        for i in (0..n).rev() {
            let cur = bytes[i] - b'a';
            // 尝试把第 i 位换成更大的字符 c
            for c in (cur + 1)..k {
                if (i >= 1 && bytes[i - 1] - b'a' == c)
                    || (i >= 2 && bytes[i - 2] - b'a' == c)
                {
                    continue; // 会造成回文，跳过
                }
                bytes[i] = b'a' + c;
                // 从 i+1 开始，逐位填入满足条件的最小字符
                for j in (i + 1)..n {
                    for d in 0..k {
                        if (j >= 1 && bytes[j - 1] - b'a' == d)
                            || (j >= 2 && bytes[j - 2] - b'a' == d)
                        {
                            continue;
                        }
                        bytes[j] = b'a' + d;
                        break;
                    }
                }
                return String::from_utf8(bytes).unwrap();
            }
        }

        // 不存在更大的美丽字符串
        String::new()
    }
}
```
