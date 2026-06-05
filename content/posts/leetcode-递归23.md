---
title: "leetcode-递归23"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 解析布尔表达式


布尔表达式 是计算结果不是 true 就是 false 的表达式。有效的表达式需遵循以下约定：

· 't'，运算结果为 true
· 'f'，运算结果为 false
· '!(subExpr)'，运算过程为对内部表达式 subExpr 进行 逻辑非（NOT）运算
· '&(subExpr1, subExpr2, ..., subExprn)'，运算过程为对 2 个或以上内部表达式 subExpr1, subExpr2, ..., subExprn 进行 逻辑与（AND）运算
· '|(subExpr1, subExpr2, ..., subExprn)'，运算过程为对 2 个或以上内部表达式 subExpr1, subExpr2, ..., subExprn 进行 逻辑或（OR）运算

给你一个以字符串形式表述的 布尔表达式 expression，返回该式的运算结果。

题目测试用例所给出的表达式均为有效的布尔表达式，遵循上述约定。

```
// 解析结果类型：Option<剩余未解析的字符串, 解析出的值>
// 'a 是生命周期参数，表示字符串引用的存活时间
type ParseResult<'a, T> = Option<(&'a str, T)>;

// 语法定义：
// true_value  :: "t"                    // 真值：字符 't'
// false_value :: "f"                    // 假值：字符 'f'
// not_expr    :: "!(" expr ")"          // 非运算：!(表达式)
// and_expr    :: "&(" expr, {"," expr}+ ")"  // 与运算：至少两个表达式，用逗号分隔
// or_expr     :: "|(" expr, {"," expr}+ ")"  // 或运算：至少两个表达式，用逗号分隔
// expr        :: true_value | false_value | not_expr | and_expr | or_expr

/// 匹配单个字符标签
/// 如果字符串以指定字符开头，则返回去掉该字符后的剩余字符串和该字符
#[inline]
fn tag(c: char, s: &str) -> ParseResult<char> {
    if s.starts_with(c) {
        Some((&s[1..], c))  // 消耗掉第一个字符，返回剩余部分
    } else {
        None
    }
}

/// 重复应用解析器，收集结果
/// p: 解析器函数，用于解析单个元素
/// init: 初始累积值
/// f: 累积函数，将当前累积值和新解析的值合并
/// s: 待解析的字符串
#[inline]
fn many<'p, 'f, 'a, S, T>(
    p: &'p mut impl FnMut(&str) -> ParseResult<T>,  // 可变的解析器函数引用
    mut init: S,                                      // 初始值（会被转移所有权）
    f: &'f mut impl FnMut(S, T) -> S,               // 累积函数
    mut s: &'a str                                   // 当前解析位置
) -> ParseResult<'a, S> {
    // 循环解析，直到解析失败
    while let Some((rest, t)) = p(s) {
        s = rest;           // 更新解析位置
        init = f(init, t);  // 累积结果
    }

    // 返回最终剩余字符串和累积结果
    Some((s, init))
}

/// 解析真值：字符 't' -> true
#[inline]
fn true_value(s: &str) -> ParseResult<bool> {
    tag('t', s).map(|(rest, _)| (rest, true))
}

/// 解析假值：字符 'f' -> false
#[inline]
fn false_value(s: &str) -> ParseResult<bool> {
    tag('f', s).map(|(rest, _)| (rest, false))
}

/// 辅助解析器：匹配逗号后跟一个表达式
/// 用于在 and_expr 和 or_expr 中解析额外的参数
fn expr_with_comma(s: &str) -> ParseResult<bool> {
    let (s, _) = tag(',', s)?;  // 匹配逗号
    let (s, v) = expr(s)?;       // 匹配后面的表达式
    Some((s, v))
}

/// 解析非运算：!(expr)
fn not_expr(s: &str) -> ParseResult<bool> {
    let (s, _) = tag('!', s)?;   // 匹配 '!'
    let (s, _) = tag('(', s)?;   // 匹配 '('
    let (s, v) = expr(s)?;       // 解析内部表达式
    let (s, _) = tag(')', s)?;   // 匹配 ')'

    Some((s, !v))  // 返回逻辑非的结果
}

/// 解析与运算：&(expr1, expr2, expr3, ...)
fn and_expr(s: &str) -> ParseResult<bool> {
    let (s, _) = tag('&', s)?;   // 匹配 '&'
    let (s, _) = tag('(', s)?;   // 匹配 '('

    // 解析第一个表达式作为初始值
    let (s, init) = expr(s)?;
    // 重复解析 ", expr" 模式，用 && 累积结果
    let (s, acc) = many(&mut expr_with_comma, init, &mut |a, b| a && b, s)?;

    let (s, _) = tag(')', s)?;   // 匹配 ')'

    Some((s, acc))  // 返回所有值的逻辑与结果
}

/// 解析或运算：|(expr1, expr2, expr3, ...)
fn or_expr(s: &str) -> ParseResult<bool> {
    let (s, _) = tag('|', s)?;   // 匹配 '|'
    let (s, _) = tag('(', s)?;   // 匹配 '('

    // 解析第一个表达式作为初始值
    let (s, init) = expr(s)?;
    // 重复解析 ", expr" 模式，用 || 累积结果
    let (s, acc) = many(&mut expr_with_comma, init, &mut |a, b| a || b, s)?;

    let (s, _) = tag(')', s)?;   // 匹配 ')'

    Some((s, acc))  // 返回所有值的逻辑或结果
}

/// 主解析器：尝试所有可能的表达式类型
fn expr(s: &str) -> ParseResult<bool> {
    true_value(s)           // 尝试解析真值
        .or_else(|| false_value(s))  // 失败则尝试假值
        .or_else(|| not_expr(s))     // 失败则尝试非运算
        .or_else(|| and_expr(s))     // 失败则尝试与运算
        .or_else(|| or_expr(s))      // 失败则尝试或运算
}

// LeetCode 要求的接口
impl Solution {
    pub fn parse_bool_expr(expression: String) -> bool {
        let (_, v) = expr(&expression).unwrap();  // 解析整个表达式，得到布尔值
        v
    }
}
```
