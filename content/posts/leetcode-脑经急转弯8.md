---
title: "leetcode-脑经急转弯8"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---

## 严格回文的数字

如果一个整数 n 在 b 进制下（b 为 2 到 n - 2 之间的所有整数）对应的字符串 全部 都是 回文的 ，那么我们称这个数 n 是 严格回文 的。

给你一个整数 n ，如果 n 是 严格回文 的，请返回 true ，否则返回 false 。

如果一个字符串从前往后读和从后往前读完全相同，那么这个字符串是 回文的 。

```
impl Solution {
    pub fn get_str(n: i32, m: i32) -> Vec<i32> {
        let mut str_m: Vec<i32> = Vec::new();
        let mut tmp_n = n;
        while tmp_n > 0 {
            str_m.push(tmp_n % m);
            tmp_n = tmp_n / m;
        }

        str_m
    }

    pub fn is_palindromic(v: Vec<i32>) -> bool {
        let mut begin = 0;
        let mut end = v.len() as i32 - 1;

        while begin <= end {
            if v[begin as usize] != v[end as usize] {
                return false;
            }
            begin += 1;
            end -= 1;
        }

        true
    }

    pub fn is_strictly_palindromic(n: i32) -> bool {
        for i in 2..n - 1 {
            let tmp_m = Self::get_str(n, i);
            if !Self::is_palindromic(tmp_m) {
                return false;
            }
        }
        true
    }
}

```
