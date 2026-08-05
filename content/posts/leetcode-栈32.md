---
title: "leetcode-栈32"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 移除无效的括号

给你一个由 '('、')' 和小写字母组成的字符串 s。

你需要从字符串中删除最少数目的 '(' 或者 ')' （可以删除任意位置的括号)，使得剩下的「括号字符串」有效。

请返回任意一个合法字符串。

有效「括号字符串」应当符合以下 任意一条 要求：

空字符串或只包含小写字母的字符串
可以被写作 AB（A 连接 B）的字符串，其中 A 和 B 都是有效「括号字符串」
可以被写作 (A) 的字符串，其中 A 是一个有效的「括号字符串」


```
impl Solution {
    pub fn min_remove_to_make_valid(s: String) -> String {
        let mut stack = Vec::with_capacity(s.len());
        let mut chars: Vec<char> = s.chars().collect();

        // 第一遍：标记需要删除的右括号
        for i in 0..chars.len() {
            match chars[i] {
                '(' => stack.push(i),
                ')' => {
                    if stack.is_empty() {
                        chars[i] = ' ';  // 标记无效右括号为空格
                    } else {
                        stack.pop();
                    }
                }
                _ => {}
            }
        }

        // 删除剩余的未匹配左括号
        while let Some(i) = stack.pop() {
            chars[i] = ' ';
        }

        // 过滤掉标记的字符
        chars.into_iter().filter(|&c| c != ' ').collect()
    }
}
```
