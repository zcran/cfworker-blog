---
title: "leetcode-字符串42"
date: 2026-10-10T12:56:54+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 作为子字符串出现在单词中的字符串数目

给你一个字符串数组 patterns 和一个字符串 word ，统计 patterns 中有多少个字符串是 word 的子字符串。返回字符串数目。

子字符串 是字符串中的一个连续字符序列。


```
impl Solution {
    pub fn num_of_strings(patterns: Vec<String>, word: String) -> i32 {
        // Rust 标准库的 str::contains 已使用高效的子串查找算法
        patterns.iter().filter(|p| word.contains(p.as_str())).count() as i32
    }
}
```


```
impl Solution {
    pub fn num_of_strings(patterns: Vec<String>, word: String) -> i32 {
        let mut res = 0;
        for pattern in patterns {
            if Self::check(&pattern, &word) {
                res += 1;
            }
        }
        res
    }

    fn check(pattern: &str, word: &str) -> bool {
        let m = pattern.len();
        let n = word.len();

        let pattern_bytes = pattern.as_bytes();
        let word_bytes = word.as_bytes();

        // 生成 pattern 的前缀数组
        let mut pi = vec![0; m];
        let mut j = 0;
        for i in 1..m {
            while j > 0 && pattern_bytes[i] != pattern_bytes[j] {
                j = pi[j - 1];
            }
            if pattern_bytes[i] == pattern_bytes[j] {
                j += 1;
            }
            pi[i] = j;
        }

        // 利用前缀数组进行匹配
        j = 0;
        for i in 0..n {
            while j > 0 && word_bytes[i] != pattern_bytes[j] {
                j = pi[j - 1];
            }
            if word_bytes[i] == pattern_bytes[j] {
                j += 1;
            }
            if j == m {
                return true;
            }
        }
        false
    }
}
```
