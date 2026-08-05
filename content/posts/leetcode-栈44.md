---
title: "leetcode-栈44"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 判断一个括号字符串是否有效

一个括号字符串是只由 '(' 和 ')' 组成的 非空 字符串。如果一个字符串满足下面 任意 一个条件，那么它就是有效的：

· 字符串为 ().
· 它可以表示为 AB（A 与 B 连接），其中A 和 B 都是有效括号字符串。
· 它可以表示为 (A) ，其中 A 是一个有效括号字符串。

给你一个括号字符串 s 和一个字符串 locked ，两者长度都为 n 。locked 是一个二进制字符串，只包含 '0' 和 '1' 。对于 locked 中 每一个 下标 i ：

· 如果 locked[i] 是 '1' ，你 不能 改变 s[i] 。
· 如果 locked[i] 是 '0' ，你 可以 将 s[i] 变为 '(' 或者 ')' 。

如果你可以将 s 变为有效括号字符串，请你返回 true ，否则返回 false 。


```
impl Solution {
    pub fn can_be_valid(s: String, locked: String) -> bool {
        let n = s.len();
        if n % 2 == 1 {
            return false; // 奇数长度不可能有效
        }

        let mut max_balance = 0; // 可达到的最大平衡度
        let mut min_balance = 0; // 可达到的最小平衡度（需满足前缀约束）

        for (i, (ch, lock)) in s.chars().zip(locked.chars()).enumerate() {
            let i = i as i32;
            if lock == '1' {
                // 字符固定，不能修改
                let diff = if ch == '(' { 1 } else { -1 };
                max_balance += diff;
                min_balance = (min_balance + diff).max((i + 1) % 2);
            } else {
                // 字符可以自由修改，选择最优方向
                max_balance += 1; // 选择 '(' 最大化平衡度
                min_balance = (min_balance - 1).max((i + 1) % 2); // 选择 ')' 最小化平衡度
            }

            // 如果最大平衡度小于最小平衡度，说明无法形成有效前缀
            if max_balance < min_balance {
                return false;
            }
        }

        // 最终平衡度必须为 0（有效括号字符串）
        min_balance == 0
    }
}
```
