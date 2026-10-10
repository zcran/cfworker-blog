---
title: "leetcode-哈希表58"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 差值数组不同的字符串

给你一个字符串数组 words ，每一个字符串长度都相同，令所有字符串的长度都为 n 。

每个字符串 words[i] 可以被转化为一个长度为 n - 1 的 差值整数数组 difference[i] ，其中对于 0 <= j <= n - 2 有 difference[i][j] = words[i][j+1] - words[i][j] 。注意两个字母的差值定义为它们在字母表中 位置 之差，也就是说 'a' 的位置是 0 ，'b' 的位置是 1 ，'z' 的位置是 25 。

比方说，字符串 "acb" 的差值整数数组是 [2 - 0, 1 - 2] = [2, -1] 。
words 中所有字符串 除了一个字符串以外 ，其他字符串的差值整数数组都相同。你需要找到那个不同的字符串。

请你返回 words中 差值整数数组 不同的字符串。




```
use std::collections::HashMap;

impl Solution {
    pub fn odd_string(words: Vec<String>) -> String {
        // 用相邻字符差值序列作为分组键；只需保存每组第一个字符串及出现次数
        // 出现次数为 1 的那组即答案
        let mut map: HashMap<Vec<i32>, (usize, usize)> = HashMap::new(); // key -> (count, index)

        for (i, s) in words.iter().enumerate() {
            let key: Vec<i32> = s
                .as_bytes()
                .windows(2)
                .map(|w| (w[1] - w[0]) as i32)
                .collect();

            let entry = map.entry(key).or_insert((0, i));
            entry.0 += 1;
        }

        // 找到出现次数为 1 的那组对应的字符串
        for (_, (count, idx)) in &map {
            if *count == 1 {
                return words[*idx].clone();
            }
        }

        unreachable!()
    }
}
```
