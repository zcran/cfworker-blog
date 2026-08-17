---
title: "leetcode-模拟30"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 竖直打印单词

给你一个字符串 s。请你按照单词在 s 中的出现顺序将它们全部竖直返回。
单词应该以字符串列表的形式返回，必要时用空格补位，但输出尾部的空格需要删除（不允许尾随空格）。
每个单词只能放在一列上，每一列中也只能有一个单词。


```
impl Solution {
    /// 将单词竖直打印：第 j 列由每个单词的第 j 个字符组成（不足补空格）
    /// 每列尾部空格需删除
    pub fn print_vertically(s: String) -> Vec<String> {
        let words: Vec<&str> = s.split_whitespace().collect();
        let max_len = words.iter().map(|w| w.len()).max().unwrap_or(0);

        (0..max_len)
            .map(|j| {
                let col: String = words
                    .iter()
                    .map(|w| w.chars().nth(j).unwrap_or(' '))
                    .collect();
                // 删除尾部空格
                col.trim_end().to_string()
            })
            .collect()
    }
}
```
