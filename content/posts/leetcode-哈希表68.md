---
title: "leetcode-哈希表68"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 统计特殊字母的数量 II

给你一个字符串 word。如果 word 中同时出现某个字母 c 的小写形式和大写形式，并且 每个 小写形式的 c 都出现在第一个大写形式的 c 之前，则称字母 c 是一个 特殊字母 。

返回 word 中 特殊字母 的数量。


```
impl Solution {
    pub fn number_of_special_chars(word: String) -> i32 {
        // 记录每个字母最后一个小写出现位置、第一个大写出现位置
        // 初值 -1 表示该形式尚未出现
        let mut last_lower = [-1i32; 26];
        let mut first_upper = [-1i32; 26];

        for (i, b) in word.bytes().enumerate() {
            let i = i as i32;
            if b.is_ascii_lowercase() {
                last_lower[(b - b'a') as usize] = i;
            } else {
                let idx = (b - b'A') as usize;
                if first_upper[idx] == -1 {
                    first_upper[idx] = i;
                }
            }
        }

        // 特殊字母：两种形式都出现，且所有小写都在第一个大写之前
        (0..26)
            .filter(|&i| {
                last_lower[i] != -1 && first_upper[i] != -1 && last_lower[i] < first_upper[i]
            })
            .count() as i32
    }
}
```
