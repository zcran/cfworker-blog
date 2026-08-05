---
title: "leetcode-栈34"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 平衡括号字符串的最少插入次数

给你一个括号字符串 s ，它只包含字符 '(' 和 ')' 。一个括号字符串被称为平衡的当它满足：

任何左括号 '(' 必须对应两个连续的右括号 '))' 。
左括号 '(' 必须在对应的连续两个右括号 '))' 之前。

比方说 "())"， "())(())))" 和 "(())())))" 都是平衡的， ")()"， "()))" 和 "(()))" 都是不平衡的。

你可以在任意位置插入字符 '(' 和 ')' 使字符串平衡。

请你返回让 s 平衡的最少插入次数。


```
impl Solution {
    pub fn min_insertions(s: String) -> i32 {
        let mut left_needed = 0;    // 需要的左括号数量
        let mut right_needed = 0;   // 当前需要的右括号数量

        for ch in s.chars() {
            if ch == '(' {
                // 遇到左括号，需要2个右括号
                // 如果当前需要的右括号是奇数，说明之前有未配对的单个右括号
                if right_needed % 2 == 1 {
                    left_needed += 1;      // 补一个左括号来匹配那个多余的右括号
                    right_needed -= 1;     // 那个右括号被匹配了
                }
                right_needed += 2;          // 新左括号需要2个右括号
            } else {
                // 遇到右括号
                if right_needed == 0 {
                    // 没有待匹配的左括号，需要插入一个左括号
                    left_needed += 1;
                    right_needed = 1;       // 这个左括号还差1个右括号
                } else {
                    right_needed -= 1;      // 匹配一个右括号
                }
            }
        }

        // 剩余的右括号需求需要插入相应数量的右括号
        left_needed + right_needed
    }
}
```
