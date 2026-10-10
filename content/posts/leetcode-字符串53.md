---
title: "leetcode-字符串53"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 强密码检验器 II

如果一个密码满足以下所有条件，我们称它是一个 强 密码：

它有至少 8 个字符。
至少包含 一个小写英文 字母。
至少包含 一个大写英文 字母。
至少包含 一个数字 。
至少包含 一个特殊字符 。特殊字符为："!@#$%^&*()-+" 中的一个。
它 不 包含 2 个连续相同的字符（比方说 "aab" 不符合该条件，但是 "aba" 符合该条件）。

给你一个字符串 password ，如果它是一个 强 密码，返回 true，否则返回 false 。




```
impl Solution {
    pub fn strong_password_checker_ii(password: String) -> bool {
        if password.len() < 8 {
            return false;
        }

        let (mut lower, mut upper, mut digit, mut special) = (false, false, false, false);
        let mut prev = 0u8;

        for b in password.bytes() {
            // 不能有连续相同字符
            if b == prev {
                return false;
            }
            prev = b;

            match b {
                b'a'..=b'z' => lower = true,
                b'A'..=b'Z' => upper = true,
                b'0'..=b'9' => digit = true,
                b'!' | b'@' | b'#' | b'$' | b'%' | b'^' | b'&' | b'*'
                | b'(' | b')' | b'-' | b'+' => special = true,
                _ => {}
            }
        }

        lower && upper && digit && special
    }
}
```
