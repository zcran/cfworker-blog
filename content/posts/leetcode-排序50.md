---
title: "leetcode-排序50"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 移除字母异位词后的结果数组

给你一个下标从 0 开始的字符串数组 words ，其中 words[i] 由小写英文字符组成。

在一步操作中，需要选出任一下标 i ，从 words 中 删除 words[i] 。其中下标 i 需要同时满足下述两个条件：

1. 0 < i < words.length
2. words[i - 1] 和 words[i] 是 字母异位词 。

只要可以选出满足条件的下标，就一直执行这个操作。

在执行所有操作后，返回 words 。可以证明，按任意顺序为每步操作选择下标都会得到相同的结果。

字母异位词 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。例如，"dacb" 是 "abdc" 的一个字母异位词。


```
impl Solution {
    /// 删除所有满足条件（与前一个单词互为字母异位词）的单词。
    ///
    /// 思路：
    /// 从左到右扫描，保留第一个单词；之后若当前单词与前一个单词
    /// 不是字母异位词，则保留，否则删除。这里"前一个单词"指的是
    /// 原数组中紧邻的前一个元素，而非保留结果中的前一个。
    /// 由于删除后相邻关系可能变化，用长度为 26 的计数数组比较即可。
    pub fn remove_anagrams(words: Vec<String>) -> Vec<String> {
        // 判断两个单词是否为字母异位词
        fn is_anagram(a: &str, b: &str) -> bool {
            if a.len() != b.len() {
                return false;
            }
            let mut freq = [0i32; 26];
            for (x, y) in a.bytes().zip(b.bytes()) {
                freq[(x - b'a') as usize] += 1;
                freq[(y - b'a') as usize] -= 1;
            }
            freq.iter().all(|&v| v == 0)
        }

        let mut res = Vec::with_capacity(words.len());
        for i in 0..words.len() {
            // 第一个单词直接保留；否则仅当不是前一单词的字母异位词时保留
            if i == 0 || !is_anagram(&words[i], &words[i - 1]) {
                res.push(words[i].clone());
            }
        }
        res
    }
}
```
