---
title: "leetcode-栈23"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 使括号有效的最少添加

只有满足下面几点之一，括号字符串才是有效的：

它是一个空字符串，或者
它可以被写成 AB （A 与 B 连接）, 其中 A 和 B 都是有效字符串，或者
它可以被写作 (A)，其中 A 是有效字符串。

给定一个括号字符串 s ，在每一次操作中，你都可以在字符串的任何位置插入一个括号

例如，如果 s = "()))" ，你可以插入一个开始括号为 "(()))" 或结束括号为 "())))" 。

返回 为使结果字符串 s 有效而必须添加的最少括号数。


```
// 最小添加使括号有效
// 维护两个计数器：需要添加的左括号数和未匹配的右括号数
impl Solution {
    pub fn min_add_to_make_valid(s: String) -> i32 {
        let mut need_left = 0; // 需要添加的左括号数（用于匹配多余的右括号）
        let mut unmatched_right = 0; // 未匹配的右括号数

        for ch in s.chars() {
            match ch {
                '(' => {
                    unmatched_right += 1; // 记录一个未匹配的左括号
                }
                ')' => {
                    if unmatched_right > 0 {
                        // 有未匹配的左括号，匹配成功
                        unmatched_right -= 1;
                    } else {
                        // 没有未匹配的左括号，需要添加一个左括号
                        need_left += 1;
                    }
                }
                _ => unreachable!(),
            }
        }

        // 需要添加的左括号数 + 剩余未匹配的右括号数
        need_left + unmatched_right
    }
}
```
