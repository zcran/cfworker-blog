---
title: "leetcode-栈30"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 反转每对括号间的子串

给出一个字符串 s（仅含有小写英文字母和括号）。

请你按照从括号内到外的顺序，逐层反转每对匹配括号中的字符串，并返回最终的结果。

注意，您的结果中 不应 包含任何括号。


```
impl Solution {
    pub fn reverse_parentheses(s: String) -> String {
        let mut stack = Vec::new();
        let mut chars: Vec<char> = s.chars().collect();

        // 一次遍历处理所有括号反转
        for i in 0..chars.len() {
            match chars[i] {
                '(' => stack.push(i),
                ')' => {
                    let start = stack.pop().unwrap();
                    // 双指针反转 start+1 到 i-1 之间的字符
                    let (mut l, mut r) = (start + 1, i - 1);
                    while l < r {
                        chars.swap(l, r);
                        l += 1;
                        r -= 1;
                    }
                }
                _ => {}
            }
        }

        // 过滤掉括号，保留最终结果
        chars.into_iter().filter(|&c| c != '(' && c != ')').collect()
    }
}
```
