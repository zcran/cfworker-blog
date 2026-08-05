---
title: "leetcode-栈1"
date: 2026-07-24T10:16:57+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。


```
use std::collections::HashMap;

impl Solution {
    pub fn is_valid(s: String) -> bool {
        // 长度奇数直接返回 false
        if s.len() % 2 == 1 {
            return false;
        }

        // 配对映射：右括号 -> 对应的左括号
        let pairs = HashMap::from([
            (')', '('),
            (']', '['),
            ('}', '{'),
        ]);

        let mut stack = Vec::with_capacity(s.len() / 2); // 预分配一半容量，节省内存

        for ch in s.chars() {
            if let Some(&expected) = pairs.get(&ch) {
                // 当前是右括号：检查栈顶是否匹配
                if stack.pop() != Some(expected) {
                    return false;
                }
            } else {
                // 当前是左括号：入栈
                stack.push(ch);
            }
        }

        stack.is_empty()
    }
}
```
