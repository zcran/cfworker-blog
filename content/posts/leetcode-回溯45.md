---
title: "leetcode-回溯45"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 口算难题

给你一个方程，左边用 words 表示，右边用 result 表示。

你需要根据以下规则检查方程是否可解：

· 每个字符都会被解码成一位数字（0 - 9）。
· 每对不同的字符必须映射到不同的数字。
· 每个 words[i] 和 result 都会被解码成一个没有前导零的数字。
· 左侧数字之和（words）等于右侧数字（result）。

如果方程可解，返回 True，否则返回 False。


```
use std::collections::HashMap;

impl Solution {
    pub fn is_solvable(words: Vec<String>, result: String) -> bool {
        // 剪枝：任何单词长度大于结果长度都不可能
        if words.iter().any(|w| w.len() > result.len()) {
            return false;
        }

        // 字符映射：字符 -> 数字，-1 表示未分配
        let mut char_map: HashMap<char, i32> = HashMap::new();
        // 前导零限制：字符不能为 0 则值为 1
        let mut leading_zero: HashMap<char, i32> = HashMap::new();

        // 初始化所有字符
        for word in &words {
            for ch in word.chars() {
                char_map.entry(ch).or_insert(-1);
                leading_zero.entry(ch).or_insert(0);
            }
            if word.len() > 1 {
                leading_zero.insert(word.chars().next().unwrap(), 1);
            }
        }
        for ch in result.chars() {
            char_map.entry(ch).or_insert(-1);
            leading_zero.entry(ch).or_insert(0);
        }
        if result.len() > 1 {
            leading_zero.insert(result.chars().next().unwrap(), 1);
        }

        let mut used_digit = [false; 10];
        let mut carry = [0; 11]; // 最多 result.len() + 1 位进位

        Self::dfs(
            &words,
            &result,
            0,      // 当前处理的位置（从低位到高位）
            0,      // 当前处理的单词索引
            &mut char_map,
            &mut used_digit,
            &mut carry,
            &leading_zero,
        )
    }

    fn dfs(
        words: &[String],
        result: &str,
        pos: usize,        // 当前位（从低位开始）
        word_idx: usize,   // 当前处理的单词索引
        char_map: &mut HashMap<char, i32>,
        used_digit: &mut [bool; 10],
        carry: &mut [i32; 11],
        leading_zero: &HashMap<char, i32>,
    ) -> bool {
        let result_bytes = result.as_bytes();
        let max_pos = result.len();

        // 所有位处理完毕，检查进位是否为 0
        if pos == max_pos {
            return carry[pos] == 0;
        }

        // 当前位还有单词未处理
        if word_idx < words.len() {
            let word = &words[word_idx];
            let word_bytes = word.as_bytes();
            let word_len = word.len();

            // 如果该单词没有这一位，跳过
            if word_len <= pos {
                return Self::dfs(words, result, pos, word_idx + 1, char_map, used_digit, carry, leading_zero);
            }

            let ch = word_bytes[word_len - pos - 1] as char;

            // 如果字符已分配，直接继续
            if let Some(&digit) = char_map.get(&ch) {
                if digit != -1 {
                    // 检查前导零
                    if digit == 0 && pos == word_len - 1 && word_len > 1 {
                        return false;
                    }
                    return Self::dfs(words, result, pos, word_idx + 1, char_map, used_digit, carry, leading_zero);
                }
            }

            // 尝试分配数字
            let min_digit = if leading_zero.get(&ch) == Some(&1) { 1 } else { 0 };
            for digit in min_digit..=9 {
                if used_digit[digit as usize] {
                    continue;
                }

                used_digit[digit as usize] = true;
                char_map.insert(ch, digit);

                if Self::dfs(words, result, pos, word_idx + 1, char_map, used_digit, carry, leading_zero) {
                    return true;
                }

                char_map.insert(ch, -1);
                used_digit[digit as usize] = false;
            }
            return false;
        }

        // 所有单词在当前位处理完毕，计算结果位
        let mut sum = carry[pos];
        for word in words {
            let word_bytes = word.as_bytes();
            if word.len() > pos {
                let ch = word_bytes[word.len() - pos - 1] as char;
                sum += char_map[&ch];
            }
        }

        carry[pos + 1] = sum / 10;
        let digit = sum % 10;

        let ch = result_bytes[result.len() - pos - 1] as char;

        // 如果结果字符已分配
        if let Some(&mapped) = char_map.get(&ch) {
            if mapped != -1 {
                if mapped != digit {
                    return false;
                }
                // 检查前导零
                if digit == 0 && pos == result.len() - 1 && result.len() > 1 {
                    return false;
                }
                return Self::dfs(words, result, pos + 1, 0, char_map, used_digit, carry, leading_zero);
            }
        }

        // 检查数字是否可用
        if used_digit[digit as usize] {
            return false;
        }

        // 前导零检查
        if digit == 0 && pos == result.len() - 1 && result.len() > 1 {
            return false;
        }

        // 分配结果字符
        char_map.insert(ch, digit);
        used_digit[digit as usize] = true;

        let valid = Self::dfs(words, result, pos + 1, 0, char_map, used_digit, carry, leading_zero);

        // 回溯
        char_map.insert(ch, -1);
        used_digit[digit as usize] = false;

        valid
    }
}
```
