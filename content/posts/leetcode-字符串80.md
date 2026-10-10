---
title: "leetcode-字符串80"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 移除尾部元音字母

给定一个由小写英文字母组成的字符串 s。

返回移除字符串 s 尾部 所有元音字母 后得到的字符串。

元音字母包括字符 'a'、'e'、'i'、'o' 和 'u'。




```
impl Solution {
    pub fn trim_trailing_vowels(s: String) -> String {
        // 找到末尾连续元音之后的位置，直接截断
        let end = s.trim_end_matches(['a', 'e', 'i', 'o', 'u']).len();
        s[..end].to_string()
    }
}
```
