---
title: "leetcode-滑动窗口99"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 元音辅音字符串计数 II

给你一个字符串 word 和一个 非负 整数 k。

返回 word 的 子字符串 中，每个元音字母（'a'、'e'、'i'、'o'、'u'）至少 出现一次，并且 恰好 包含 k 个辅音字母的子字符串的总数。


```
impl Solution {
    pub fn count_of_substrings(word: String, k: i32) -> i64 {
        let word = word.as_bytes();
        let k = k as usize;

        // 元音判断表
        let mut is_vowel = [false; 128];
        for &ch in b"aeiou" {
            is_vowel[ch as usize] = true;
        }

        // 计算"至少包含 k 个辅音"的子字符串数量
        // 使用差分技巧：恰好 k 个 = 至少 k 个 - 至少 (k+1) 个
        let at_least = |min_consonants: usize| -> i64 {
            let mut ans = 0;
            let mut vowel_freq = [0; 26];
            let mut vowel_types = 0;
            let mut consonant_count = 0;
            let mut left = 0;

            for right in 0..word.len() {
                let ch = word[right];

                // 扩展右指针
                if is_vowel[ch as usize] {
                    let idx = (ch - b'a') as usize;
                    if vowel_freq[idx] == 0 {
                        vowel_types += 1;
                    }
                    vowel_freq[idx] += 1;
                } else {
                    consonant_count += 1;
                }

                // 收缩左指针直到满足条件
                while vowel_types == 5 && consonant_count >= min_consonants {
                    // 以当前 left 为起点，right 为终点，所有更右的右端点都合法
                    ans += (word.len() - right) as i64;

                    let left_ch = word[left];
                    if is_vowel[left_ch as usize] {
                        let idx = (left_ch - b'a') as usize;
                        vowel_freq[idx] -= 1;
                        if vowel_freq[idx] == 0 {
                            vowel_types -= 1;
                        }
                    } else {
                        consonant_count -= 1;
                    }
                    left += 1;
                }
            }

            ans
        };

        // 恰好 k 个 = 至少 k 个 - 至少 (k+1) 个
        at_least(k) - at_least(k + 1)
    }
}
```
