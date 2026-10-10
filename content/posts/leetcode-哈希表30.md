---
title: "leetcode-哈希表30"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 查找共用字符

给你一个字符串数组 words ，请你找出所有在 words 的每个字符串中都出现的共用字符（包括重复字符），并以数组形式返回。你可以按 任意顺序 返回答案。


```
impl Solution {
    pub fn common_chars(words: Vec<String>) -> Vec<String> {
        // 对每个字母取所有单词中出现次数的最小值，即为公共字符的数量。
        // 用 26 长度的计数数组表示当前公共字符的最小频次，边遍历边收缩。
        let mut min_freq = [usize::MAX; 26];

        for word in &words {
            let mut freq = [0usize; 26];
            for b in word.bytes() {
                freq[(b - b'a') as usize] += 1;
            }
            for i in 0..26 {
                min_freq[i] = min_freq[i].min(freq[i]);
            }
        }

        // 展开为结果：每个字母出现 min_freq[i] 次。
        let mut result = Vec::new();
        for (i, &cnt) in min_freq.iter().enumerate() {
            for _ in 0..cnt {
                result.push(((b'a' + i as u8) as char).to_string());
            }
        }
        result
    }
}
```
