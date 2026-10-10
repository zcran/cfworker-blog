---
title: "leetcode-字符串10"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 检测大写字母

我们定义，在以下情况时，单词的大写用法是正确的：

全部字母都是大写，比如 "USA" 。
所有字母都不是大写，比如 "leetcode" 。
只有首字母大写， 比如 "Google" 。

给你一个字符串 word 。如果大写用法正确，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn detect_capital_use(word: String) -> bool {
        // 统计大写字母个数
        let upper = word.bytes().filter(u8::is_ascii_uppercase).count();
        // 三种合法情况：全小写、全大写、仅首字母大写
        upper == 0
            || upper == word.len()
            || (upper == 1 && word.as_bytes()[0].is_ascii_uppercase())
    }
}
```
