---
title: "leetcode-哈希表18"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 最短补全词

给你一个字符串 licensePlate 和一个字符串数组 words ，请你找出 words 中的 最短补全词 。

补全词 是一个包含 licensePlate 中所有字母的单词。忽略 licensePlate 中的 数字和空格 。不区分大小写。如果某个字母在 licensePlate 中出现不止一次，那么该字母在补全词中的出现次数应当一致或者更多。

例如：licensePlate = "aBc 12c"，那么它的补全词应当包含字母 'a'、'b' （忽略大写）和两个 'c' 。可能的 补全词 有 "abccdef"、"caaacab" 以及 "cbca" 。

请返回 words 中的 最短补全词 。题目数据保证一定存在一个最短补全词。当有多个单词都符合最短补全词的匹配条件时取 words 中 第一个 出现的那个。


```
impl Solution {
    pub fn shortest_completing_word(license_plate: String, words: Vec<String>) -> String {
        // 统计字符串中各字母出现次数（忽略大小写、数字和空格）。
        fn count(s: &str) -> [u8; 26] {
            let mut tab = [0u8; 26];
            for c in s.bytes() {
                match c {
                    b'a'..=b'z' => tab[(c - b'a') as usize] += 1,
                    b'A'..=b'Z' => tab[(c - b'A') as usize] += 1,
                    _ => {}
                }
            }
            tab
        }

        // 补全词判定：word 的每个字母计数都不少于 licensePlate。
        let need = count(&license_plate);
        let mut ans: Option<String> = None;

        for w in words {
            // 仅当更短（严格小于）时才替换，天然保留首次出现的最短词。
            if ans.as_ref().is_some_and(|a| a.len() <= w.len()) {
                continue;
            }
            let have = count(&w);
            if need.iter().zip(have.iter()).all(|(&n, &h)| h >= n) {
                ans = Some(w);
            }
        }

        ans.unwrap()
    }
}
```
