---
title: "leetcode-递归21"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 基本计算器 IV


给定一个表达式如 expression = "e + 8 - a + 5" 和一个求值映射，如 {"e": 1}（给定的形式为 evalvars = ["e"] 和 evalints = [1]），返回表示简化表达式的标记列表，例如 ["-1*a","14"]

· 表达式交替使用块和符号，每个块和符号之间有一个空格。
· 块要么是括号中的表达式，要么是变量，要么是非负整数。
· 变量是一个由小写字母组成的字符串（不包括数字）。请注意，变量可以是多个字母，并注意变量从不具有像 "2x" 或 "-x" 这样的前导系数或一元运算符 。

表达式按通常顺序进行求值：先是括号，然后求乘法，再计算加法和减法。

· 例如，expression = "1 + 2 * 3" 的答案是 ["7"]。

输出格式如下：

· 对于系数非零的每个自变量项，我们按字典排序的顺序将自变量写在一个项中。
    · 例如，我们永远不会写像 “b*a\*c” 这样的项，只写 “a\*b\*c”。
· 项的次数等于被乘的自变量的数目，并计算重复项。我们先写出答案的最大次数项，用字典顺序打破关系，此时忽略词的前导系数。
    · 例如，"a\*a\*b\*c" 的次数为 4。
· 项的前导系数直接放在左边，用星号将它与变量分隔开(如果存在的话)。前导系数 1 仍然要打印出来。
· 格式良好的一个示例答案是 ["-2\*a\*a\*a", "3\*a\*a\*b", "3\*b\*b", "4\*a", "5\*c", "-6"] 。
· 系数为 0 的项（包括常数项）不包括在内。
    · 例如，“0” 的表达式输出为 [] 。

注意：你可以假设给定的表达式均有效。所有中间结果都在区间 [-231, 231 - 1] 内。



示例 1：

输入：expression = "e + 8 - a + 5", evalvars = ["e"], evalints = [1]
输出：["-1\*a","14"]
示例 2：

输入：expression = "e - 8 + temperature - pressure",
evalvars = ["e", "temperature"], evalints = [1, 12]
输出：["-1\*pressure","5"]
示例 3：

输入：expression = "(e + 8) * (e - 8)", evalvars = [], evalints = []
输出：["1\*e\*e","-64"]


```
use std::collections::{BTreeMap, HashMap};

/// 多项式：以排序后的变量列表为键，系数为值
#[derive(Debug, Clone, Default)]
struct Poly {
    coeffs: BTreeMap<Vec<String>, i32>,
}

impl Poly {
    fn new() -> Self {
        Poly::default()
    }

    fn update(&mut self, key: Vec<String>, val: i32) {
        if val == 0 {
            return;
        }
        let entry = self.coeffs.entry(key.clone()).or_insert(0);
        *entry += val;
        if *entry == 0 {
            self.coeffs.remove(&key);
        }
    }

    fn add(mut self, other: Poly) -> Self {
        for (key, val) in other.coeffs {
            self.update(key, val);
        }
        self
    }

    fn sub(mut self, other: Poly) -> Self {
        for (key, val) in other.coeffs {
            self.update(key, -val);
        }
        self
    }

    fn mul(self, other: Poly) -> Self {
        let mut result = Poly::new();
        for (key1, val1) in &self.coeffs {
            for (key2, val2) in &other.coeffs {
                let mut new_key = key1.clone();
                new_key.extend(key2.clone());
                new_key.sort();
                result.update(new_key, val1 * val2);
            }
        }
        result
    }

    fn evaluate(self, eval_map: &HashMap<String, i32>) -> Self {
        let mut result = Poly::new();
        for (key, val) in self.coeffs {
            let mut coeff = val;
            let mut remaining_vars = Vec::new();

            for var in key {
                if let Some(&eval_val) = eval_map.get(&var) {
                    coeff *= eval_val;
                } else {
                    remaining_vars.push(var);
                }
            }

            if coeff != 0 {
                result.update(remaining_vars, coeff);
            }
        }
        result
    }

    fn to_vector(self) -> Vec<String> {
        let mut items: Vec<(Vec<String>, i32)> = self.coeffs.into_iter().collect();

        items.sort_by(|a, b| {
            let a_len = a.0.len();
            let b_len = b.0.len();
            if a_len != b_len {
                b_len.cmp(&a_len)
            } else {
                a.0.cmp(&b.0)
            }
        });

        items
            .into_iter()
            .map(|(vars, coeff)| {
                let mut term = coeff.to_string();
                for var in vars {
                    term.push('*');
                    term.push_str(&var);
                }
                term
            })
            .collect()
    }
}

struct Parser;

impl Parser {
    fn make_atom(expr: &str) -> Poly {
        let mut poly = Poly::new();
        if let Ok(num) = expr.parse::<i32>() {
            poly.update(Vec::new(), num);
        } else {
            poly.update(vec![expr.to_string()], 1);
        }
        poly
    }

    fn combine(left: Poly, right: Poly, op: char) -> Poly {
        match op {
            '+' => left.add(right),
            '-' => left.sub(right),
            '*' => left.mul(right),
            _ => panic!("未知运算符: {}", op),
        }
    }

    fn parse(expr: &str) -> Poly {
        let chars: Vec<char> = expr.chars().collect();
        let mut i = 0;
        let mut bucket: Vec<Poly> = Vec::new();
        let mut symbols: Vec<char> = Vec::new();

        while i < chars.len() {
            let c = chars[i];

            if c == ' ' {
                i += 1;
                continue;
            }

            if c == '(' {
                // 找到匹配的右括号
                let mut paren_count = 1;
                let mut j = i + 1;
                while j < chars.len() && paren_count > 0 {
                    if chars[j] == '(' {
                        paren_count += 1;
                    } else if chars[j] == ')' {
                        paren_count -= 1;
                    }
                    j += 1;
                }
                // 递归解析括号内的表达式（不包括外层括号）
                let sub_expr = &expr[i + 1..j - 1];
                bucket.push(Self::parse(sub_expr));
                i = j;
                continue;
            }

            if c == '+' || c == '-' || c == '*' {
                symbols.push(c);
                i += 1;
                continue;
            }

            // 读取操作数（数字或变量）
            let start = i;
            while i < chars.len() && chars[i] != ' ' && chars[i] != '(' && chars[i] != ')' {
                i += 1;
            }
            let token = &expr[start..i];
            bucket.push(Self::make_atom(token));
        }

        // 先处理乘法
        let mut j = symbols.len() as isize - 1;
        while j >= 0 {
            if symbols[j as usize] == '*' {
                let right = bucket.remove(j as usize + 1);
                let left = bucket.remove(j as usize);
                bucket.insert(j as usize, Self::combine(left, right, '*'));
                symbols.remove(j as usize);
            }
            j -= 1;
        }

        // 再处理加减法
        if bucket.is_empty() {
            return Poly::new();
        }

        let mut result = bucket.remove(0);
        for (idx, &op) in symbols.iter().enumerate() {
            let next = bucket.remove(0);
            result = Self::combine(result, next, op);
        }

        result
    }
}

impl Solution {
    pub fn basic_calculator_iv(
        expression: String,
        evalvars: Vec<String>,
        evalints: Vec<i32>,
    ) -> Vec<String> {
        let eval_map: HashMap<String, i32> = evalvars
            .into_iter()
            .zip(evalints.into_iter())
            .collect();

        let poly = Parser::parse(&expression);
        poly.evaluate(&eval_map).to_vector()
    }
}
```
