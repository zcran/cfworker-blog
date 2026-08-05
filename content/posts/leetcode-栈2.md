---
title: "leetcode-栈2"
date: 2026-07-24T10:16:57+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 最长有效括号

给你一个只包含 '(' 和 ')' 的字符串，找出最长有效（格式正确且连续）括号 子串 的长度。

左右括号匹配，即每个左括号都有对应的右括号将其闭合的字符串是格式正确的，比如 "(()())"。


```
impl Solution {
    pub fn longest_valid_parentheses(s: String) -> i32 {
        let bytes = s.as_bytes();
        let mut stack = Vec::with_capacity(bytes.len());
        let mut max_len = 0;

        // 栈底存储最后一个未匹配的右括号索引，初始为 -1
        stack.push(-1);

        for (i, &ch) in bytes.iter().enumerate() {
            if ch == b'(' {
                stack.push(i as i32);
            } else {
                stack.pop();
                if stack.is_empty() {
                    // 当前右括号无法匹配，作为新的分隔点
                    stack.push(i as i32);
                } else {
                    // 计算当前有效子串长度
                    let len = i as i32 - stack.last().unwrap();
                    max_len = max_len.max(len);
                }
            }
        }

        max_len
    }
}
```
