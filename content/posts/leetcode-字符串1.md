---
title: "leetcode-字符串1"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## Z 字形变换

将一个给定字符串 s 根据给定的行数 numRows ，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 "PAYPALISHIRING" 行数为 3 时，排列如下：

P   A   H   N
A P L S I I G
Y   I   R

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："PAHNAPLSIIGYIR"。

请你实现这个将字符串进行指定行数变换的函数：

string convert(string s, int numRows);


```
impl Solution {
    pub fn convert(s: String, num_rows: i32) -> String {
        let n = s.len();
        let num_rows = num_rows as usize;

        // 边界情况：只有一行，或行数不小于字符串长度，直接返回
        if num_rows == 1 || num_rows >= n {
            return s;
        }

        let mut rows = vec![String::with_capacity(n); num_rows];
        // 周期：每个 Z 字形循环包含 2 * num_rows - 2 个字符
        let cycle = 2 * num_rows - 2;
        // 预分配容量，减少每行的扩容次数（每行大约 n / cycle * 2 个字符）
        let per_row = (n + cycle - 1) / cycle * 2;

        for (i, c) in s.chars().enumerate() {
            // 计算当前字符在 Z 字形中的行号（比模拟上下往复更快）
            let pos = i % cycle;
            let row = if pos < num_rows { pos } else { cycle - pos };
            rows[row].push(c);
        }

        rows.concat()
    }
}
```
