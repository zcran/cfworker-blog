---
title: "leetcode-栈8"
date: 2026-07-24T10:16:57+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 基本计算器 II

给你一个字符串表达式 s ，请你实现一个基本计算器来计算并返回它的值。

整数除法仅保留整数部分。

你可以假设给定的表达式总是有效的。所有中间结果将在 [-231, 231 - 1] 的范围内。

注意：不允许使用任何将字符串作为数学表达式计算的内置函数，比如 eval() 。


```
// 表达式求值：支持 + - * /，无括号
// 使用单栈法，遇到乘除立即计算，最后累加加减结果
impl Solution {
    pub fn calculate(s: String) -> i32 {
        let mut stack = Vec::new();
        let mut current_num = 0;
        let mut operator = '+'; // 当前数字前的运算符

        // 添加哨兵字符，简化最后数字处理
        for ch in s.chars().chain(std::iter::once('+')) {
            match ch {
                ' ' => continue,
                '0'..='9' => {
                    current_num = current_num * 10 + (ch as u8 - b'0') as i32;
                }
                _ => {
                    // 根据前一个运算符处理当前数字
                    match operator {
                        '+' => stack.push(current_num),
                        '-' => stack.push(-current_num),
                        '*' => {
                            let last = stack.last_mut().unwrap();
                            *last *= current_num;
                        }
                        '/' => {
                            let last = stack.last_mut().unwrap();
                            *last /= current_num;
                        }
                        _ => unreachable!(),
                    }
                    // 更新运算符和重置数字
                    operator = ch;
                    current_num = 0;
                }
            }
        }

        stack.iter().sum()
    }
}
```
