---
title: "leetcode-字符串32"
date: 2026-10-10T12:56:53+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 设计 Goal 解析器

请你设计一个可以解释字符串 command 的 Goal 解析器 。command 由 "G"、"()" 和/或 "(al)" 按某种顺序组成。Goal 解析器会将 "G" 解释为字符串 "G"、"()" 解释为字符串 "o" ，"(al)" 解释为字符串 "al" 。然后，按原顺序将经解释得到的字符串连接成一个字符串。

给你字符串 command ，返回 Goal 解析器 对 command 的解释结果。




```
impl Solution {
    pub fn interpret(command: String) -> String {
        // 一次遍历：'G' 直接输出，'(' 看下一字符决定是 "o" 还是 "al"
        let mut result = String::with_capacity(command.len());
        let mut chars = command.chars().peekable();

        while let Some(c) = chars.next() {
            match c {
                'G' => result.push('G'),
                '(' => {
                    if chars.peek() == Some(&')') {
                        chars.next(); // 消耗 ')'
                        result.push('o');
                    } else {
                        // 形如 "(al)"，跳过 "al)" 并输出 "al"
                        chars.next(); // 'a'
                        chars.next(); // 'l'
                        chars.next(); // ')'
                        result.push_str("al");
                    }
                }
                _ => {}
            }
        }

        result
    }
}
```
