---
title: "leetcode-栈21"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 括号的分数

给定一个平衡括号字符串 S，按下述规则计算该字符串的分数：

() 得 1 分。

AB 得 A + B 分，其中 A 和 B 是平衡括号字符串。

(A) 得 2 * A 分，其中 A 是平衡括号字符串。


```
// 平衡括号字符串评分
// 使用栈模拟：每个 '(' 创建新层，遇到 ')' 计算当前层分数
impl Solution {
    pub fn score_of_parentheses(s: String) -> i32 {
        let mut stack = Vec::with_capacity(s.len() / 2 + 1);
        stack.push(0); // 栈底存储总分

        for ch in s.bytes() {
            if ch == b'(' {
                stack.push(0); // 新的一层
            } else {
                // 计算当前层分数
                let score = stack.pop().unwrap();
                // 规则：() = 1, (A) = 2 * A
                let value = if score == 0 { 1 } else { score * 2 };
                *stack.last_mut().unwrap() += value;
            }
        }

        stack[0]
    }
}
```
