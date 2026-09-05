---
title: "leetcode-前缀和78"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计范围内的元音字符串数

给你一个下标从 0 开始的字符串数组 words 以及一个二维整数数组 queries 。

每个查询 queries[i] = [li, ri] 会要求我们统计在 words 中下标在 li 到 ri 范围内（包含 这两个值）并且以元音开头和结尾的字符串的数目。

返回一个整数数组，其中数组的第 i 个元素对应第 i 个查询的答案。

注意：元音字母是 'a'、'e'、'i'、'o' 和 'u' 。


```
impl Solution {
    pub fn vowel_strings(words: Vec<String>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        // 元音集合，使用字节数组提高查找效率
        const VOWELS: [u8; 5] = [b'a', b'e', b'i', b'o', b'u'];

        // 判断字符串是否以元音开头和结尾
        fn is_vowel_string(s: &str) -> bool {
            let bytes = s.as_bytes();
            VOWELS.contains(&bytes[0]) && VOWELS.contains(&bytes[bytes.len() - 1])
        }

        // 构建前缀和数组
        let n = words.len();
        let mut prefix = vec![0; n + 1];
        for (i, word) in words.iter().enumerate() {
            prefix[i + 1] = prefix[i] + is_vowel_string(word) as i32;
        }

        // 处理每个查询
        queries
            .iter()
            .map(|q| prefix[q[1] as usize + 1] - prefix[q[0] as usize])
            .collect()
    }
}
```
