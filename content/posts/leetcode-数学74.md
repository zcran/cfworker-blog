---
title: "leetcode-数学74"
date: 2026-10-07T20:39:33+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 十六进制和三十六进制转化

给你一个整数 n。

返回 n^2 的 十六进制表示 和 n^3 的 三十六进制表示 拼接成的字符串。

十六进制 数定义为使用数字 0 – 9 和大写字母 A - F 表示 0 到 15 的值。

三十六进制 数定义为使用数字 0 – 9 和大写字母 A - Z 表示 0 到 35 的值。


```
impl Solution {
    pub fn concat_hex36(n: i32) -> String {
        // n² 的十六进制：标准库 {:X} 直接输出大写十六进制
        let hex = format!("{:X}", n * n);

        // n³ 的三十六进制：逐位取余，0-9 映射为 '0'-'9'，10-35 映射为 'A'-'Z'
        let mut v = n * n * n;
        let mut base36 = Vec::new();
        while v > 0 {
            let d = (v % 36) as u8;
            base36.push(if d < 10 { b'0' + d } else { b'A' + d - 10 });
            v /= 36;
        }
        base36.reverse();

        // 拼接：n² 的十六进制 + n³ 的三十六进制
        let mut result = hex.into_bytes();
        result.extend(base36);
        String::from_utf8(result).unwrap()
    }
}
```
