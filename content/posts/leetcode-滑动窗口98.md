---
title: "leetcode-滑动窗口98"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 元音辅音字符串计数 I

给你一个字符串 word 和一个 非负 整数 k。

返回 word 的 子字符串 中，每个元音字母（'a'、'e'、'i'、'o'、'u'）至少 出现一次，并且 恰好 包含 k 个辅音字母的子字符串的总数。


```
impl Solution {
    pub fn count_of_substrings(word: String, k: i32) -> i32 {
        let word = word.as_bytes();
        let n = word.len();
        let k = k as usize;

        // 元音标记数组：快速判断字符是否为元音
        let mut is_vowel = [false; 128];
        for &ch in b"aeiou" {
            is_vowel[ch as usize] = true;
        }

        let mut ans = 0;
        let mut vowel_count = [0; 26]; // 元音字符频率
        let mut consonant_count = 0;
        let mut vowel_types = 0; // 窗口中出现的元音种类数
        let mut left = 0;

        // 滑动窗口：右指针扩展
        for right in 0..n {
            let ch = word[right];

            // 1. 将右端字符加入窗口
            if is_vowel[ch as usize] {
                let idx = (ch - b'a') as usize;
                if vowel_count[idx] == 0 {
                    vowel_types += 1;
                }
                vowel_count[idx] += 1;
            } else {
                consonant_count += 1;
            }

            // 2. 当辅音数量超过 k 时，收缩左指针
            while consonant_count > k {
                let left_ch = word[left];
                if is_vowel[left_ch as usize] {
                    let idx = (left_ch - b'a') as usize;
                    vowel_count[idx] -= 1;
                    if vowel_count[idx] == 0 {
                        vowel_types -= 1;
                    }
                } else {
                    consonant_count -= 1;
                }
                left += 1;
            }

            // 3. 当满足条件（5种元音齐全且辅音数 = k）时，计数
            if vowel_types == 5 && consonant_count == k {
                // 统计以当前 right 结尾，且满足条件的左边界数量
                let mut temp_left = left;
                let mut temp_vowel_count = vowel_count;
                let mut temp_vowel_types = vowel_types;
                let mut temp_consonant_count = consonant_count;

                while temp_left <= right && temp_vowel_types == 5 && temp_consonant_count == k {
                    ans += 1;
                    let left_ch = word[temp_left];
                    if is_vowel[left_ch as usize] {
                        let idx = (left_ch - b'a') as usize;
                        temp_vowel_count[idx] -= 1;
                        if temp_vowel_count[idx] == 0 {
                            temp_vowel_types -= 1;
                        }
                    } else {
                        temp_consonant_count -= 1;
                    }
                    temp_left += 1;
                }
            }
        }

        ans
    }
}
```
