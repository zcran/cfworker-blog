---
title: "leetcode-栈27"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 删除最外层的括号

有效括号字符串为空 ""、"(" + A + ")" 或 A + B ，其中 A 和 B 都是有效的括号字符串，+ 代表字符串的连接。

例如，""，"()"，"(())()" 和 "(()(()))" 都是有效的括号字符串。

如果有效字符串 s 非空，且不存在将其拆分为 s = A + B 的方法，我们称其为原语（primitive），其中 A 和 B 都是非空有效括号字符串。

给出一个非空有效字符串 s，考虑将其进行原语化分解，使得：s = P_1 + P_2 + ... + P_k，其中 P_i 是有效括号字符串原语。

对 s 进行原语化分解，删除分解中每个原语字符串的最外层括号，返回 s 。


```
impl Solution {
    pub fn remove_outer_parentheses(s: String) -> String {
        let mut depth = 0;          // 当前括号嵌套深度
        let mut result = String::with_capacity(s.len());  // 预分配内存

        for ch in s.chars() {
            match ch {
                '(' => {
                    // 只有非最外层的 '(' 才保留（深度 > 0 时说明已有外层括号）
                    if depth > 0 {
                        result.push(ch);
                    }
                    depth += 1;
                }
                ')' => {
                    depth -= 1;
                    // 只有非最外层的 ')' 才保留（深度 > 0 说明括号未闭合）
                    if depth > 0 {
                        result.push(ch);
                    }
                }
                _ => unreachable!(), // 题目保证输入只包含括号
            }
        }

        result
    }
}
```
