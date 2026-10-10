---
title: "leetcode-哈希表24"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 单词子集

给你两个字符串数组 words1 和 words2。

现在，如果 b 中的每个字母都出现在 a 中，包括重复出现的字母，那么称字符串 b 是字符串 a 的 子集 。

例如，"wrr" 是 "warrior" 的子集，但不是 "world" 的子集。

如果对 words2 中的每一个单词 b，b 都是 a 的子集，那么我们称 words1 中的单词 a 是 通用单词 。

以数组形式返回 words1 中所有的 通用 单词。你可以按 任意顺序 返回答案。


```
impl Solution {
    pub fn word_subsets(words1: Vec<String>, words2: Vec<String>) -> Vec<String> {
        // 统计 words2 中每个字母出现的最大次数，即 words1 中单词必须满足的下限。
        let mut need = [0u8; 26];
        for w in &words2 {
            let mut cnt = [0u8; 26];
            for c in w.bytes() {
                cnt[(c - b'a') as usize] += 1;
            }
            for i in 0..26 {
                need[i] = need[i].max(cnt[i]);
            }
        }

        // 单词满足所有字母计数不低于 need 即为通用单词。
        words1
            .into_iter()
            .filter(|w| {
                let mut cnt = [0u8; 26];
                for c in w.bytes() {
                    cnt[(c - b'a') as usize] += 1;
                }
                cnt.iter().zip(&need).all(|(&c, &n)| c >= n)
            })
            .collect()
    }
}
```
