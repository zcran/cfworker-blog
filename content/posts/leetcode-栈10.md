---
title: "leetcode-栈10"
date: 2026-07-24T10:16:57+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 迷你语法分析器

给定一个字符串 s 表示一个整数嵌套列表，实现一个解析它的语法分析器并返回解析的结果 NestedInteger 。

列表中的每个元素只可能是整数或整数嵌套列表


```
// 解析嵌套整数列表
// 使用递归下降解析器，直接操作字符流，无需显式词法分析
impl Solution {
    pub fn deserialize(s: String) -> NestedInteger {
        let mut chars = s.chars().peekable();
        parse_expr(&mut chars)
    }
}

// 解析表达式：整数或列表
fn parse_expr(chars: &mut std::iter::Peekable<std::str::Chars>) -> NestedInteger {
    match chars.peek() {
        Some('[') => parse_list(chars),
        Some('-') | Some('0'..='9') => parse_integer(chars),
        _ => unreachable!("Invalid input"),
    }
}

// 解析列表：[expr, expr, ...]
fn parse_list(chars: &mut std::iter::Peekable<std::str::Chars>) -> NestedInteger {
    chars.next(); // 跳过 '['
    let mut list = NestedInteger::List(vec![]);

    // 处理空列表
    if let Some(&']') = chars.peek() {
        chars.next();
        return list;
    }

    // 解析第一个元素
    if let NestedInteger::List(ref mut items) = list {
        items.push(parse_expr(chars));
    }

    // 解析后续元素
    while let Some(&',') = chars.peek() {
        chars.next(); // 跳过 ','
        if let NestedInteger::List(ref mut items) = list {
            items.push(parse_expr(chars));
        }
    }

    // 期望 ']'
    if let Some(&']') = chars.peek() {
        chars.next();
    }

    list
}

// 解析整数：[-]数字
fn parse_integer(chars: &mut std::iter::Peekable<std::str::Chars>) -> NestedInteger {
    let mut negative = false;

    // 处理负号
    if let Some(&'-') = chars.peek() {
        negative = true;
        chars.next();
    }

    // 解析数字
    let mut num = 0;
    while let Some(&ch) = chars.peek() {
        if ch.is_ascii_digit() {
            num = num * 10 + (ch as u8 - b'0') as i32;
            chars.next();
        } else {
            break;
        }
    }

    NestedInteger::Int(if negative { -num } else { num })
}
```
