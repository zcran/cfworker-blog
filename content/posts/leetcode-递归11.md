---
title: "leetcode-递归11"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 基本计算器

给你一个字符串表达式 s ，请你实现一个基本计算器来计算并返回它的值。

注意:不允许使用任何将字符串作为数学表达式计算的内置函数，比如 eval() 。

```
impl Solution {
    /// 公开入口方法：计算算术表达式的值
    pub fn calculate(s: String) -> i32 {
        let mut parser = Parser::new(&s);
        parser.parse_expr().unwrap_or(0)  // 解析表达式，失败则默认返回0
    }
}

/// 递归下降解析器，用于计算算术表达式
/// 支持：+、-、括号、多位数
struct Parser<'a> {
    input: &'a [u8],  // 输入的字节数组，便于高效查看字符
    cursor: usize,     // 当前解析位置
}

impl<'a> Parser<'a> {
    /// 创建新的解析器，并跳过开头的空白字符
    fn new(input: &'a str) -> Self {
        let mut res = Self { input: input.as_bytes(), cursor: 0 };
        res.skip_spaces();
        res
    }

    /// 查看当前字符（不消费）
    fn peek(&self) -> Option<&u8> {
        self.input.get(self.cursor)
    }

    /// 消费当前字符（光标后移）
    fn next(&mut self) {
        self.cursor += 1;
    }

    /// 跳过所有连续的空格
    fn skip_spaces(&mut self) {
        while let Some(b' ') = self.peek() {
            self.next();
        }
    }

    /// 尝试解析指定的符号（如 '+', '-', '(', ')'）
    /// 成功则返回 Some(())，失败返回 None
    fn parse_symbol(&mut self, symbol: u8) -> Option<()> {
        if self.peek() == Some(&symbol) {
            self.next();           // 消费掉该符号
            self.skip_spaces();    // 跳过后面的空格
            Some(())
        } else {
            None
        }
    }

    /// 解析多位数字（非负整数）
    /// 如果没有数字则返回 None
    fn parse_number(&mut self) -> Option<i32> {
        let mut num = 0;
        let cur = self.cursor;  // 记住开始位置

        // 循环读取数字字符
        while let Some(&c) = self.peek() {
            if c.is_ascii_digit() {
                num *= 10;
                num += (c - b'0') as i32;  // ASCII 数字转换为数值
                self.next();
            } else {
                break;
            }
        }

        if self.cursor == cur {
            None  // 没有读取到任何数字
        } else {
            self.skip_spaces();  // 跳过后面的空格
            Some(num)
        }
    }

    /// 解析一元负号运算符（如 "-5" 或 "-(2+3)"）
    /// 文法：unary = '-' atom | number
    fn parse_unary(&mut self) -> Option<i32> {
        if self.parse_symbol(b'-').is_some() {
            // 遇到一元负号，解析后面的原子并取负
            self.parse_atom().map(|x| -x)
        } else {
            // 没有一元运算符，直接解析数字
            self.parse_number()
        }
    }

    /// 解析原子表达式：要么是括号表达式，要么是一元表达式
    /// 文法：atom = '(' expr ')' | unary
    fn parse_atom(&mut self) -> Option<i32> {
        let res;
        if self.parse_symbol(b'(').is_some() {
            // 解析括号表达式：'(' expr ')'
            res = self.parse_expr()?;   // 解析括号内的表达式
            self.parse_symbol(b')')?;   // 期望一个右括号
        } else {
            // 解析一元表达式（数字或负号开头的表达式）
            res = self.parse_unary()?;
        }
        Some(res)
    }

    /// 解析加减法表达式（左结合）
    /// 文法：expr = atom (('+' | '-') atom)*
    fn parse_expr(&mut self) -> Option<i32> {
        // 解析第一个原子（数字、括号表达式或一元表达式）
        let mut ans = self.parse_atom()?;

        // 循环处理后面的 + 和 - 运算符
        loop {
            if self.parse_symbol(b'+').is_some() {
                ans += self.parse_atom()?;  // 加上下一个原子
            } else if self.parse_symbol(b'-').is_some() {
                ans -= self.parse_atom()?;  // 减去下一个原子
            } else {
                break;  // 没有更多运算符，结束
            }
        }
        Some(ans)
    }
}
```
