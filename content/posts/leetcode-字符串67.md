---
title: "leetcode-字符串67"
date: 2026-10-10T12:56:57+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 使三个字符串相等

给你三个字符串 s1、s2 和 s3。 你可以根据需要对这三个字符串执行以下操作 任意次数 。

在每次操作中，你可以选择其中一个长度至少为 2 的字符串 并删除其 最右位置上 的字符。

如果存在某种方法能够使这三个字符串相等，请返回使它们相等所需的 最小 操作次数；否则，返回 -1。




```
impl Solution {
    pub fn find_minimum_operations(s1: String, s2: String, s3: String) -> i32 {
        let (a, b, c) = (s1.as_bytes(), s2.as_bytes(), s3.as_bytes());

        // 求三个字符串的最长公共前缀长度
        let lcp = a
            .iter()
            .zip(b.iter())
            .zip(c.iter())
            .take_while(|&((&x, &y), &z)| x == y && y == z)
            .count();

        if lcp == 0 {
            return -1;
        }

        // 总长度减去保留的公共前缀部分
        (a.len() + b.len() + c.len() - 3 * lcp) as i32
    }
}
```
