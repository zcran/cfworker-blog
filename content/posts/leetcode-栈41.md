---
title: "leetcode-栈41"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 删除一个字符串中所有出现的给定子字符串

给你两个字符串 s 和 part ，请你对 s 反复执行以下操作直到 所有 子字符串 part 都被删除：

找到 s 中 最左边 的子字符串 part ，并将它从 s 中删除。

请你返回从 s 中删除所有 part 子字符串以后得到的剩余字符串。

一个 子字符串 是一个字符串中连续的字符序列。


```
impl Solution {
    pub fn remove_occurrences(s: String, part: String) -> String {
        let part_chars: Vec<char> = part.chars().collect();
        let m = part_chars.len();
        let mut stack: Vec<char> = Vec::with_capacity(s.len());

        for ch in s.chars() {
            stack.push(ch);
            // 检查栈顶是否形成了 part
            if stack.len() >= m {
                let start = stack.len() - m;
                if stack[start..] == part_chars[..] {
                    // 删除匹配的部分
                    stack.truncate(start);
                }
            }
        }

        stack.into_iter().collect()
    }
}
```
