---
title: "leetcode-字符串15"
date: 2026-10-10T12:56:52+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 山羊拉丁文

给你一个由若干单词组成的句子 sentence ，单词间由空格分隔。每个单词仅由大写和小写英文字母组成。

请你将句子转换为 “山羊拉丁文（Goat Latin）”（一种类似于 猪拉丁文 - Pig Latin 的虚构语言）。山羊拉丁文的规则如下：

· 如果单词以元音开头（'a', 'e', 'i', 'o', 'u'），在单词后添加"ma"。
    · 例如，单词 "apple" 变为 "applema" 。
· 如果单词以辅音字母开头（即，非元音字母），移除第一个字符并将它放到末尾，之后再添加"ma"。
    · 例如，单词 "goat" 变为 "oatgma" 。
· 根据单词在句子中的索引，在单词最后添加与索引相同数量的字母'a'，索引从 1 开始。
    · 例如，在第一个单词后添加 "a" ，在第二个单词后添加 "aa" ，以此类推。

返回将 sentence 转换为山羊拉丁文后的句子。


```
impl Solution {
    pub fn to_goat_latin(sentence: String) -> String {
        let mut result = String::with_capacity(sentence.len() * 2);

        for (i, word) in sentence.split_ascii_whitespace().enumerate() {
            if i > 0 {
                result.push(' ');
            }
            let bytes = word.as_bytes();
            let first = bytes[0].to_ascii_lowercase();
            // 元音开头：原样；辅音开头：首字母移到末尾
            if matches!(first, b'a' | b'e' | b'i' | b'o' | b'u') {
                result.push_str(word);
            } else {
                result.push_str(&word[1..]);
                result.push(bytes[0] as char);
            }
            // 统一追加 "ma" 和 i+1 个 'a'
            result.push_str("ma");
            for _ in 0..=i {
                result.push('a');
            }
        }

        result
    }
}
```
