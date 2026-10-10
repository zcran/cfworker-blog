---
title: "leetcode-排序44"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 将句子排序

一个 句子 指的是一个序列的单词用单个空格连接起来，且开头和结尾没有任何空格。每个单词都只包含小写或大写英文字母。

我们可以给一个句子添加 从 1 开始的单词位置索引 ，并且将句子中所有单词 打乱顺序 。

比方说，句子 "This is a sentence" 可以被打乱顺序得到 "sentence4 a3 is2 This1" 或者 "is2 sentence4 This1 a3" 。

给你一个 打乱顺序 的句子 s ，它包含的单词不超过 9 个，请你重新构造并得到原本顺序的句子。


```
impl Solution {
    /// 重新构造原本顺序的句子。
    ///
    /// 思路：
    /// 每个单词末尾带有一个 1..=9 的位置索引，单词本身不含数字。
    /// 直接按末尾索引将单词放入对应位置，再按顺序拼接即可。
    pub fn sort_sentence(s: String) -> String {
        let mut words = vec![String::new(); 9];

        for token in s.split(' ') {
            // token 形如 "word3"，最后一位是位置索引
            let idx = (token.as_bytes()[token.len() - 1] - b'1') as usize;
            words[idx] = token[..token.len() - 1].to_string();
        }

        // 按位置顺序拼接，过滤掉未使用的空槽
        words
            .into_iter()
            .filter(|w| !w.is_empty())
            .collect::<Vec<_>>()
            .join(" ")
    }
}
```
