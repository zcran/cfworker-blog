---
title: "leetcode-字符串29"
date: 2026-10-10T12:56:53+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 重新排列单词间的空格

给你一个字符串 text ，该字符串由若干被空格包围的单词组成。每个单词由一个或者多个小写英文字母组成，并且两个单词之间至少存在一个空格。题目测试用例保证 text 至少包含一个单词 。

请你重新排列空格，使每对相邻单词之间的空格数目都 相等 ，并尽可能 最大化 该数目。如果不能重新平均分配所有空格，请 将多余的空格放置在字符串末尾 ，这也意味着返回的字符串应当与原 text 字符串的长度相等。

返回 重新排列空格后的字符串 。


```
impl Solution {
    pub fn reorder_spaces(text: String) -> String {
        let words: Vec<&str> = text.split_ascii_whitespace().collect();
        let space_cnt = text.len() - words.iter().map(|w| w.len()).sum::<usize>();

        // 单词数为 1 时无法在单词间分配空格，全部放末尾
        let gap = if words.len() > 1 { space_cnt / (words.len() - 1) } else { 0 };
        let trailing = space_cnt - gap * (words.len() - 1);

        // 预分配与原串等长，避免多次扩容
        let mut result = String::with_capacity(text.len());
        for (i, word) in words.iter().enumerate() {
            if i > 0 {
                result.push_str(&" ".repeat(gap));
            }
            result.push_str(word);
        }
        result.push_str(&" ".repeat(trailing));
        result
    }
}
```
