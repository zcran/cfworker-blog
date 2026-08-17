---
title: "leetcode-模拟128"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 用特殊操作处理字符串 II

给你一个字符串 s，由小写英文字母和特殊字符：'*'、'#' 和 '%' 组成。

同时给你一个整数 k。

请根据以下规则从左到右处理 s 中每个字符，构造一个新的字符串 result：

如果字符是 小写 英文字母，则将其添加到 result 中。
字符 '*' 会 删除 result 中的最后一个字符（如果存在）。
字符 '#' 会 复制 当前的 result 并追加到其自身后面。
字符 '%' 会 反转 当前的 result。

返回最终字符串 result 中第 k 个字符（下标从 0 开始）。如果 k 超出 result 的下标索引范围，则返回 '.'。


```
impl Solution {
    pub fn process_str(s: String, mut k: i64) -> char {
        let mut len: i64 = 0;
        for c in s.chars() {
            match c {
                '*' => {
                    if len > 0 {
                        len -= 1;
                    }
                }
                '#' => {
                    len *= 2;
                }
                '%' => {}
                _ => {
                    len += 1;
                }
            }
        }
        if k + 1 > len {
            return '.';
        }
        for c in s.chars().rev() {
            match c {
                '*' => {
                    len += 1;
                }
                '#' => {
                    if k + 1 > (len + 1) / 2 {
                        k -= len / 2;
                    }
                    len = (len + 1) / 2;
                }
                '%' => {
                    k = len - k - 1;
                }
                _ => {
                    if k + 1 == len {
                        return c;
                    }
                    len -= 1;
                }
            }
        }
        '.'
    }
}
```
