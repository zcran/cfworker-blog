---
title: "leetcode-模拟8"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 字符串相加

给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和并同样以字符串形式返回。

你不能使用任何內建的用于处理大整数的库（比如 BigInteger）， 也不能直接将输入的字符串转换为整数形式。


```
impl Solution {
    pub fn add_strings(num1: String, num2: String) -> String {
        let (n1, n2) = (num1.as_bytes(), num2.as_bytes());
        let (mut i, mut j) = (n1.len(), n2.len());
        let mut carry = 0;
        let mut ans = String::with_capacity(i.max(j) + 1);

        while i > 0 || j > 0 || carry > 0 {
            if i > 0 { i -= 1; carry += (n1[i] - b'0') as i32; }
            if j > 0 { j -= 1; carry += (n2[j] - b'0') as i32; }
            ans.push((b'0' + (carry % 10) as u8) as char);
            carry /= 10;
        }

        ans.chars().rev().collect()
    }
}
```
