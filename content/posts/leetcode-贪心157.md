---
title: "leetcode-贪心157"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 转换字符串的最小操作次数

给你一个仅由小写英文字母组成的字符串 s。

你可以执行以下操作任意次（包括零次）：

选择字符串中出现的一个字符 c，并将 每个 出现的 c 替换为英文字母表中 下一个 小写字母。

返回将 s 转换为仅由 'a' 组成的字符串所需的最小操作次数。

注意：字母表是循环的，因此 'z' 的下一个字母是 'a'。




```
impl Solution {
    pub fn min_operations(s: String) -> i32 {
        // 每次操作把某个字符 c 全部变成它的下一个字母。
        // 要让 s 全为 'a'，需从最小的非 'a' 字符出发逐级递增到 'z' 再变 'a'。
        // 所需操作次数 = 'z' + 1 - (s 中最小的非 'a' 字符)。
        // 若全是 'a'，最小非 'a' 字符记为 'z' + 1，结果为 0。
        let mut min_c = b'z' + 1;
        for b in s.bytes() {
            if b != b'a' {
                if b < min_c {
                    min_c = b;
                    if min_c == b'b' {
                        break;
                    }
                }
            }
        }
        (b'z' + 1 - min_c) as i32
    }
}
```
