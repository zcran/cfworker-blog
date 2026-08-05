---
title: "leetcode-枚举5"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 超级回文数

如果一个正整数自身是回文数，而且它也是一个回文数的平方，那么我们称这个数为 超级回文数 。

现在，给你两个以字符串形式表示的正整数 left 和 right  ，统计并返回区间 [left, right] 中的 超级回文数 的数目。


```
impl Solution {
    /// 统计区间 [left, right] 内的超级回文数数量。
    /// 超级回文数：自身是回文数，且是某个回文数的平方。
    pub fn superpalindromes_in_range(left: String, right: String) -> i32 {
        let left = left.parse::<i64>().unwrap();
        let right = right.parse::<i64>().unwrap();

        let mut count = 0;
        let limit = 100_000; // 构造回文根的半长度上限，保证平方不超过 i64 范围

        // 1. 构造偶数长度回文根（如 123321）
        for half in 1..limit {
            let root = build_palindrome_even(half);
            let square = root.saturating_mul(root);
            if square > right { break; }
            if square >= left && is_palindrome(square) {
                count += 1;
            }
        }

        // 2. 构造奇数长度回文根（如 12321）
        for half in 1..limit {
            let root = build_palindrome_odd(half);
            let square = root.saturating_mul(root);
            if square > right { break; }
            if square >= left && is_palindrome(square) {
                count += 1;
            }
        }

        count
    }
}

/// 将数字 half 转换为字符串，并拼接其反转，形成偶数长度回文数（如 12 -> 1221）
#[inline]
fn build_palindrome_even(half: i64) -> i64 {
    let s = half.to_string();
    let rev = s.chars().rev().collect::<String>();
    format!("{}{}", s, rev).parse::<i64>().unwrap()
}

/// 将数字 half 转换为字符串，并拼接其反转（去掉第一个字符），形成奇数长度回文数（如 12 -> 121）
#[inline]
fn build_palindrome_odd(half: i64) -> i64 {
    let s = half.to_string();
    let rev = s.chars().rev().skip(1).collect::<String>();
    format!("{}{}", s, rev).parse::<i64>().unwrap()
}

/// 判断一个整数是否为回文数（无需转为字符串）
#[inline]
fn is_palindrome(mut num: i64) -> bool {
    if num < 0 { return false; }
    let original = num;
    let mut reversed = 0;
    while num > 0 {
        reversed = reversed * 10 + (num % 10);
        num /= 10;
    }
    reversed == original
}
```
