---
title: "leetcode-栈40"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 反转表达式值的最少操作次数

给你一个 有效的 布尔表达式，用字符串 expression 表示。这个字符串包含字符 '1'，'0'，'&'（按位 与 运算），'|'（按位 或 运算），'(' 和 ')' 。

比方说，"()1|1" 和 "(1)&()" 不是有效 布尔表达式。而 "1"， "(((1))|(0))" 和 "1|(0&(1))" 是 有效 布尔表达式。

你的目标是将布尔表达式的 值 反转 （也就是将 0 变为 1 ，或者将 1 变为 0），请你返回达成目标需要的 最少操作 次数。

比方说，如果表达式 expression = "1|1|(0&0)&1" ，它的 值 为 1|1|(0&0)&1 = 1|1|0&1 = 1|0&1 = 1&1 = 1 。我们想要执行操作将 新的 表达式的值变成 0 。

可执行的 操作 如下：

将一个 '1' 变成一个 '0' 。
将一个 '0' 变成一个 '1' 。
将一个 '&' 变成一个 '|' 。
将一个 '|' 变成一个 '&' 。

注意：'&' 的 运算优先级 与 '|' 相同 。计算表达式时，括号优先级 最高 ，然后按照 从左到右 的顺序运算。


```
impl Solution {
    pub fn min_operations_to_flip(expression: String) -> i32 {
        // 数字栈：每个元素 (cost_to_0, cost_to_1) 表示将当前子表达式变为 0 或 1 的最小操作数
        let mut num_stack: Vec<(i32, i32)> = Vec::with_capacity(expression.len());
        // 符号栈：存储 '(', '|', '&'
        let mut op_stack: Vec<char> = Vec::with_capacity(expression.len());

        for ch in expression.chars() {
            match ch {
                '(' | '|' | '&' => op_stack.push(ch),
                '0' => {
                    num_stack.push((0, 1));
                    // 尝试结合
                    while num_stack.len() >= 2 && !op_stack.is_empty() {
                        let op = *op_stack.last().unwrap();
                        if op != '|' && op != '&' {
                            break;
                        }
                        // 弹出右操作数（先弹）和左操作数
                        let (x1, y1) = num_stack.pop().unwrap();
                        let (x2, y2) = num_stack.pop().unwrap();
                        op_stack.pop();

                        // 计算保持 '&' 的结果
                        let x_and = (x1 + x2).min(x1 + y2).min(y1 + x2);
                        let y_and = y1 + y2;

                        // 计算保持 '|' 的结果
                        let x_or = x1 + x2;
                        let y_or = (x1 + y2).min(y1 + x2).min(y1 + y2);

                        // 根据当前运算符，比较保持原运算符和翻转运算符（+1 次操作）
                        let (x, y) = if op == '&' {
                            (x_and.min(x_or + 1), y_and.min(y_or + 1))
                        } else {
                            (x_or.min(x_and + 1), y_or.min(y_and + 1))
                        };
                        num_stack.push((x, y));
                    }
                }
                '1' => {
                    num_stack.push((1, 0));
                    // 尝试结合
                    while num_stack.len() >= 2 && !op_stack.is_empty() {
                        let op = *op_stack.last().unwrap();
                        if op != '|' && op != '&' {
                            break;
                        }
                        let (x1, y1) = num_stack.pop().unwrap();
                        let (x2, y2) = num_stack.pop().unwrap();
                        op_stack.pop();

                        let x_and = (x1 + x2).min(x1 + y2).min(y1 + x2);
                        let y_and = y1 + y2;

                        let x_or = x1 + x2;
                        let y_or = (x1 + y2).min(y1 + x2).min(y1 + y2);

                        let (x, y) = if op == '&' {
                            (x_and.min(x_or + 1), y_and.min(y_or + 1))
                        } else {
                            (x_or.min(x_and + 1), y_or.min(y_and + 1))
                        };
                        num_stack.push((x, y));
                    }
                }
                ')' => {
                    op_stack.pop(); // 弹出对应的 '('
                    // 尝试结合
                    while num_stack.len() >= 2 && !op_stack.is_empty() {
                        let op = *op_stack.last().unwrap();
                        if op != '|' && op != '&' {
                            break;
                        }
                        let (x1, y1) = num_stack.pop().unwrap();
                        let (x2, y2) = num_stack.pop().unwrap();
                        op_stack.pop();

                        let x_and = (x1 + x2).min(x1 + y2).min(y1 + x2);
                        let y_and = y1 + y2;

                        let x_or = x1 + x2;
                        let y_or = (x1 + y2).min(y1 + x2).min(y1 + y2);

                        let (x, y) = if op == '&' {
                            (x_and.min(x_or + 1), y_and.min(y_or + 1))
                        } else {
                            (x_or.min(x_and + 1), y_or.min(y_and + 1))
                        };
                        num_stack.push((x, y));
                    }
                }
                _ => unreachable!(),
            }
        }

        let (x, y) = num_stack[0];
        x.max(y)
    }
}
```
