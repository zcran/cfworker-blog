---
title: "leetcode-栈7"
date: 2026-07-24T10:16:57+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 逆波兰表达式求值

给你一个字符串数组 tokens ，表示一个根据 逆波兰表示法 表示的算术表达式。

请你计算该表达式。返回一个表示表达式值的整数。

注意：

有效的算符为 '+'、'-'、'*' 和 '/' 。
每个操作数（运算对象）都可以是一个整数或者另一个表达式。
两个整数之间的除法总是 向零截断 。
表达式中不含除零运算。
输入是一个根据逆波兰表示法表示的算术表达式。
答案及所有中间计算结果可以用 32 位 整数表示。


```
// 逆波兰表达式求值
// 遇到数字入栈，遇到运算符弹出两个操作数计算后压回
use std::collections::VecDeque;

impl Solution {
    pub fn eval_rpn(tokens: Vec<String>) -> i32 {
        let mut stack = Vec::with_capacity(tokens.len());

        for token in tokens {
            // 尝试解析为数字
            if let Ok(num) = token.parse::<i32>() {
                stack.push(num);
            } else {
                // 运算符：弹出右操作数，再弹出左操作数
                let right = stack.pop().unwrap();
                let left = stack.pop().unwrap();

                let result = match token.as_bytes()[0] {
                    b'+' => left + right,
                    b'-' => left - right,
                    b'*' => left * right,
                    b'/' => left / right, // Rust 整数除法默认向零截断
                    _ => unreachable!(),
                };

                stack.push(result);
            }
        }

        stack.pop().unwrap()
    }
}
```
