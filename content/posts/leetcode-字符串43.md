---
title: "leetcode-字符串43"
date: 2026-10-10T12:56:54+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 检查句子中的数字是否递增

句子是由若干 token 组成的一个列表，token 间用 单个 空格分隔，句子没有前导或尾随空格。每个 token 要么是一个由数字 0-9 组成的不含前导零的 正整数 ，要么是一个由小写英文字母组成的 单词 。

示例，"a puppy has 2 eyes 4 legs" 是一个由 7 个 token 组成的句子："2" 和 "4" 是数字，其他像 "puppy" 这样的 tokens 属于单词。

给你一个表示句子的字符串 s ，你需要检查 s 中的 全部 数字是否从左到右严格递增（即，除了最后一个数字，s 中的 每个 数字都严格小于它 右侧 的数字）。

如果满足题目要求，返回 true ，否则，返回 false 。


```
impl Solution {
    pub fn are_numbers_ascending(s: String) -> bool {
        // 只保留数字 token，逐个比较是否严格递增，无需收集成 Vec
        let mut prev = 0;
        for token in s.split_ascii_whitespace() {
            if let Ok(n) = token.parse::<i32>() {
                if n <= prev {
                    return false;
                }
                prev = n;
            }
        }
        true
    }
}
```
