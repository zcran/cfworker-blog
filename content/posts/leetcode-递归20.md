---
title: "leetcode-递归20"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## Lisp 语法解析

给你一个类似 Lisp 语句的字符串表达式 expression，求出其计算结果。

表达式语法如下所示:

· 表达式可以为整数，let 表达式，add 表达式，mult 表达式，或赋值的变量。表达式的结果总是一个整数。
· (整数可以是正整数、负整数、0)
· let 表达式采用 "(let v1 e1 v2 e2 ... vn en expr)" 的形式，其中 let 总是以字符串 "let"来表示，接下来会跟随一对或多对交替的变量和表达式，也就是说，第一个变量 v1被分配为表达式 e1 的值，第二个变量 v2 被分配为表达式 e2 的值，依次类推；最终 let 表达式的值为 expr表达式的值。
· add 表达式表示为 "(add e1 e2)" ，其中 add 总是以字符串 "add" 来表示，该表达式总是包含两个表达式 e1、e2 ，最终结果是 e1 表达式的值与 e2 表达式的值之 和 。
· mult 表达式表示为 "(mult e1 e2)" ，其中 mult 总是以字符串 "mult" 表示，该表达式总是包含两个表达式 e1、e2，最终结果是 e1 表达式的值与 e2 表达式的值之 积 。
· 在该题目中，变量名以小写字符开始，之后跟随 0 个或多个小写字符或数字。为了方便，"add" ，"let" ，"mult" 会被定义为 "关键字" ，不会用作变量名。
· 最后，要说一下作用域的概念。计算变量名所对应的表达式时，在计算上下文中，首先检查最内层作用域（按括号计），然后按顺序依次检查外部作用域。测试用例中每一个表达式都是合法的。有关作用域的更多详细信息，请参阅示例。

示例 1：

输入：expression = "(let x 2 (mult x (let x 3 y 4 (add x y))))"
输出：14
解释：
计算表达式 (add x y), 在检查变量 x 值时，
在变量的上下文中由最内层作用域依次向外检查。
首先找到 x = 3, 所以此处的 x 值是 3 。
示例 2：

输入：expression = "(let x 3 x 2 x)"
输出：2
解释：let 语句中的赋值运算按顺序处理即可。
示例 3：

输入：expression = "(let x 1 y 2 x (add x y) (add x y))"
输出：5
解释：
第一个 (add x y) 计算结果是 3，并且将此值赋给了 x 。
第二个 (add x y) 计算结果是 3 + 2 = 5 。




```
impl Solution {
    /// 计算 Lisp 风格表达式的值
    ///
    /// 支持的表达式类型：
    /// - 整数：直接返回数值
    /// - 变量：返回变量的值
    /// - (add e1 e2)：返回 e1 + e2
    /// - (mult e1 e2)：返回 e1 * e2
    /// - (let v1 e1 v2 e2 ... expr)：依次绑定变量，返回最后一个表达式的值
    ///
    /// 示例：
    /// evaluate("(add 1 2)") = 3
    /// evaluate("(let x 3 (add x 1))") = 4
    pub fn evaluate(expression: String) -> i32 {
        let mut tokens = Tokens::new(expression.as_str());  // 创建词法分析器
        let mut env = Environment::new();                   // 创建变量环境
        eval(&mut tokens, &mut env)                         // 解析并求值
    }
}

use std::collections::HashMap;

/// 递归求值函数
/// tokens: 词法分析器（迭代器）
/// env: 变量环境（存储变量的值）
fn eval<'a>(tokens: &mut Tokens<'a>, env: &mut Environment<'a>) -> i32 {
    match tokens.next().unwrap() {
        // 情况1：数字，直接返回
        Token::Num(n) => n,

        // 情况2：变量，从环境中查找其值
        Token::Var(v) => env.get(v),

        // 情况3：遇到 '('，开始解析复合表达式
        Token::Start => match tokens.next().unwrap() {
            // 3.1：add 表达式
            Token::Add => {
                let left = eval(tokens, env);   // 计算左操作数
                let right = eval(tokens, env);  // 计算右操作数
                let res = left + right;          // 相加
                assert_eq!(Token::End, tokens.next().unwrap());  // 匹配 ')'
                res
            }

            // 3.2：mult 表达式
            Token::Mul => {
                let left = eval(tokens, env);   // 计算左操作数
                let right = eval(tokens, env);  // 计算右操作数
                let res = left * right;          // 相乘
                assert_eq!(Token::End, tokens.next().unwrap());  // 匹配 ')'
                res
            }

            // 3.3：let 表达式（最复杂）
            Token::Let => {
                let mut vars_to_pop = vec![];  // 记录需要清理的变量（用于作用域管理）

                loop {
                    match tokens.peek().unwrap() {
                        // 遇到变量名
                        Token::Var(v) => {
                            tokens.next().unwrap();  // 消费变量名

                            // 检查是否是最后一个表达式（变量名后直接跟 ')'）
                            if tokens.peek().unwrap() == Token::End {
                                // 返回变量的值
                                let res = env.get(v);
                                // 清理所有临时变量（恢复作用域）
                                for var in vars_to_pop {
                                    env.pop(var);
                                }
                                assert_eq!(Token::End, tokens.next().unwrap());  // 匹配 ')'
                                return res;
                            } else {
                                // 否则，计算变量绑定的值
                                let val = eval(tokens, env);
                                // 将变量绑定到当前作用域
                                env.put(v, val);
                                vars_to_pop.push(v);  // 记录以便后续清理
                            }
                        }

                        // 遇到表达式（let 的最后一部分）
                        _ => {
                            // 计算最后一个表达式的值
                            let res = eval(tokens, env);
                            // 清理所有临时变量
                            for var in vars_to_pop {
                                env.pop(var);
                            }
                            assert_eq!(Token::End, tokens.next().unwrap());  // 匹配 ')'
                            return res;
                        }
                    }
                }
            }

            _ => panic!("Invalid expr after '('"),
        },

        _ => panic!("Invalid start of expression"),
    }
}

/// 词法分析器（将字符串转换为 Token 流）
struct Tokens<'a> {
    expr: &'a [u8],   // 表达式字节数组
    start: usize,     // 当前 token 的开始位置
    end: usize,       // 当前 token 的结束位置
}

/// Token 类型
#[derive(Debug, PartialEq, Eq)]
enum Token<'a> {
    Start,   // '('
    End,     // ')'
    Add,     // "add"
    Mul,     // "mult"
    Let,     // "let"
    Num(i32),      // 数字
    Var(&'a [u8]), // 变量名
}

impl<'a> Tokens<'a> {
    /// 创建新的词法分析器
    fn new(expr: &'a str) -> Self {
        Tokens {
            expr: expr.as_bytes(),
            start: 0,
            end: 0,
        }
    }

    /// 查看下一个 token（不消费）
    fn peek(&mut self) -> Option<Token<'a>> {
        let start = self.start;
        let end = self.end;
        let res = self.next();  // 先获取下一个 token
        self.start = start;     // 恢复位置
        self.end = end;
        res
    }
}

impl<'a> Iterator for Tokens<'a> {
    type Item = Token<'a>;

    /// 获取下一个 token（消费）
    fn next(&mut self) -> Option<Self::Item> {
        // 已到达末尾
        if self.end == self.expr.len() {
            return None;
        }

        // 跳过空格
        while self.expr[self.end] == b' ' {
            self.end += 1;
            if self.end == self.expr.len() {
                return None;
            }
        }

        self.start = self.end;
        self.end += 1;

        // 处理括号
        match self.expr[self.start] {
            b'(' => return Some(Token::Start),
            b')' => return Some(Token::End),
            _ => {}
        }

        // 读取完整的 token（直到遇到空格或右括号）
        while !(self.expr[self.end] == b' ' || self.expr[self.end] == b')') {
            self.end += 1;
            if self.end == self.expr.len() {
                return None;
            }
        }

        // 判断是数字还是关键字/变量
        if self.expr[self.start] == b'-'
            || (self.expr[self.start] >= b'0' && self.expr[self.start] <= b'9')
        {
            // 解析数字
            let as_str = unsafe { std::str::from_utf8_unchecked(&self.expr[self.start..self.end]) };
            return Some(Token::Num(as_str.parse::<i32>().unwrap()));
        }

        // 解析关键字或变量名
        let token = match &self.expr[self.start..self.end] {
            b"mult" => Token::Mul,
            b"add" => Token::Add,
            b"let" => Token::Let,
            var => Token::Var(var),
        };
        Some(token)
    }
}

/// 变量环境（管理作用域）
struct Environment<'a> {
    vars: HashMap<&'a [u8], Vec<i32>>,  // 变量名 -> 值栈（支持嵌套作用域）
}

impl<'a> Environment<'a> {
    /// 创建新的空环境
    fn new() -> Self {
        Environment {
            vars: HashMap::new(),
        }
    }

    /// 获取变量的当前值（栈顶）
    fn get(&self, var: &[u8]) -> i32 {
        self.vars.get(var).map(|v| v[v.len() - 1]).unwrap()
    }

    /// 将变量绑定到新值（压栈）
    fn put(&mut self, var: &'a [u8], val: i32) {
        let entry = self.vars.entry(var).or_insert(vec![]);
        (*entry).push(val);
    }

    /// 弹出变量的最内层绑定（恢复作用域）
    fn pop(&mut self, var: &[u8]) {
        self.vars.get_mut(var).map(|v| v.pop().unwrap()).unwrap();
    }
}
```
