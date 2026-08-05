---
title: "leetcode-滑动窗口2"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 串联所有单词的子串

给定一个字符串 s 和一个字符串数组 words。 words 中所有字符串 长度相同。

 s 中的 串联子串 是指一个包含  words 中所有字符串以任意顺序排列连接起来的子串。

例如，如果 words = ["ab","cd","ef"]， 那么 "abcdef"， "abefcd"，"cdabef"， "cdefab"，"efabcd"， 和 "efcdab" 都是串联子串。 "acdbef" 不是串联子串，因为他不是任何 words 排列的连接。

返回所有串联子串在 s 中的开始索引。你可以以 任意顺序 返回答案。


```
use std::collections::HashMap;

impl Solution {
    /// 找到所有串联子串的起始索引
    ///
    /// 滑动窗口 + 哈希表计数，按单词长度分组扫描
    /// 时间复杂度: O(n * word_len), 空间复杂度: O(words.len())
    pub fn find_substring(s: String, words: Vec<String>) -> Vec<i32> {
        if words.is_empty() || s.len() < words.len() * words[0].len() {
            return vec![];
        }

        let word_len = words[0].len();
        let word_count = words.len();
        let window_len = word_len * word_count;
        let s_bytes = s.as_bytes();

        // 目标词频统计
        let mut target = HashMap::with_capacity(word_count);
        for w in &words {
            *target.entry(w.as_str()).or_insert(0) += 1;
        }

        let mut result = Vec::new();

        // 按起始偏移分组扫描，避免遗漏
        for offset in 0..word_len {
            let mut seen = HashMap::with_capacity(word_count);
            let mut valid_count = 0; // 当前窗口中计数匹配的单词种类数
            let mut left = offset;

            // right 指向当前单词的起始位置
            for right in (offset..=s_bytes.len() - word_len).step_by(word_len) {
                let word = std::str::from_utf8(&s_bytes[right..right + word_len]).unwrap();

                // 扩展窗口
                if let Some(&target_count) = target.get(word) {
                    let count = seen.entry(word).or_insert(0);
                    *count += 1;

                    if *count == target_count {
                        valid_count += 1;
                    } else if *count == target_count + 1 {
                        valid_count -= 1;
                    }
                } else {
                    // 遇到非法单词，重置窗口
                    seen.clear();
                    valid_count = 0;
                    left = right + word_len;
                    continue;
                }

                // 窗口大小达到要求
                if right - left + word_len == window_len {
                    if valid_count == target.len() {
                        result.push(left as i32);
                    }

                    // 移除左边界单词
                    let left_word = std::str::from_utf8(&s_bytes[left..left + word_len]).unwrap();
                    if let Some(&target_count) = target.get(left_word) {
                        let count = seen.get_mut(left_word).unwrap();
                        if *count == target_count {
                            valid_count -= 1;
                        } else if *count == target_count + 1 {
                            valid_count += 1;
                        }
                        *count -= 1;
                    }
                    left += word_len;
                }
            }
        }

        result
    }
}
```
