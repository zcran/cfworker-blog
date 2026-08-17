---
title: "leetcode-模拟4"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 二进制求和

给你两个二进制字符串 a 和 b ，以二进制字符串的形式返回它们的和。


```
impl Solution {
    pub fn add_binary(a: String, b: String) -> String {
        let (a, b) = (a.as_bytes(), b.as_bytes());
        let (mut i, mut j) = (a.len(), b.len());
        let mut carry = 0;
        let mut ans = String::with_capacity(i.max(j) + 1);

        while i > 0 || j > 0 || carry > 0 {
            if i > 0 { i -= 1; carry += (a[i] - b'0') as i32; }
            if j > 0 { j -= 1; carry += (b[j] - b'0') as i32; }
            ans.push((b'0' + (carry & 1) as u8) as char);
            carry >>= 1;
        }

        ans.chars().rev().collect()
    }
}
```
