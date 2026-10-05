---
title: "leetcode-贪心1"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 通配符匹配

给你一个输入字符串 (s) 和一个字符模式 (p) ，请你实现一个支持 '?' 和 '*' 匹配规则的通配符匹配：

'?' 可以匹配任何单个字符。

'*' 可以匹配任意字符序列（包括空字符序列）。

判定匹配成功的充要条件是：字符模式必须能够 完全匹配 输入字符串（而不是部分匹配）。


```
impl Solution {
    pub fn is_match(s: String, p: String) -> bool {
        let (s, p) = (s.as_bytes(), p.as_bytes());
        let (mut i, mut j) = (0, 0);

        // 最近一次遇到的 '*' 在 p 中的位置，以及它当时匹配到 s 的位置
        let mut star: Option<usize> = None;
        let mut star_match = 0;

        while i < s.len() {
            // 单字符匹配：'?' 或字符相等
            if j < p.len() && (p[j] == b'?' || p[j] == s[i]) {
                i += 1;
                j += 1;
            }
            // 遇到 '*'：先假设它匹配空串，记录回溯点
            else if j < p.len() && p[j] == b'*' {
                star = Some(j);
                star_match = i;
                j += 1;
            }
            // 失配但有 '*' 可回溯：让 '*' 多吞一个字符
            else if let Some(star_pos) = star {
                star_match += 1;
                i = star_match;
                j = star_pos + 1;
            }
            // 无法匹配
            else {
                return false;
            }
        }

        // s 已耗尽，p 剩余部分必须全是 '*'
        while j < p.len() && p[j] == b'*' {
            j += 1;
        }

        j == p.len()
    }
}
```
