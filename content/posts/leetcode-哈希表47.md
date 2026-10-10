---
title: "leetcode-哈希表47"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 替换字符串中的括号内容

给你一个字符串 s ，它包含一些括号对，每个括号中包含一个 非空 的键。

比方说，字符串 "(name)is(age)yearsold" 中，有 两个 括号对，分别包含键 "name" 和 "age" 。

你知道许多键对应的值，这些关系由二维字符串数组 knowledge 表示，其中 knowledge[i] = [keyi, valuei] ，表示键 keyi 对应的值为 valuei 。

你需要替换 所有 的括号对。当你替换一个括号对，且它包含的键为 keyi 时，你需要：

将 keyi 和括号用对应的值 valuei 替换。

如果从 knowledge 中无法得知某个键对应的值，你需要将 keyi 和括号用问号 "?" 替换（不需要引号）。

knowledge 中每个键最多只会出现一次。s 中不会有嵌套的括号。

请你返回替换 所有 括号对后的结果字符串。


```
use std::collections::HashMap;

impl Solution {
    pub fn evaluate(s: String, knowledge: Vec<Vec<String>>) -> String {
        // 键值表：键 → 值。
        let map: HashMap<&str, &str> = knowledge
            .iter()
            .map(|kv| (kv[0].as_str(), kv[1].as_str()))
            .collect();

        let mut ans = String::with_capacity(s.len());
        let bytes = s.as_bytes();
        let mut i = 0;

        while i < bytes.len() {
            match bytes[i] {
                b'(' => {
                    // 定位右括号，取出键并替换为对应值或 "?"。
                    let end = bytes[i + 1..].iter().position(|&b| b == b')').unwrap() + i + 1;
                    let key = &s[i + 1..end];
                    ans.push_str(map.get(key).copied().unwrap_or("?"));
                    i = end + 1;
                }
                // 非括号内容按字符原样追加（正确处理多字节 UTF-8）。
                _ => {
                    let ch = s[i..].chars().next().unwrap();
                    ans.push(ch);
                    i += ch.len_utf8();
                }
            }
        }

        ans
    }
}
```
