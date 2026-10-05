---
title: "leetcode-位运算13"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 数字转换为十六进制数

给定一个整数，编写一个算法将这个数转换为十六进制数。对于负整数，我们通常使用 补码运算 方法。

答案字符串中的所有字母都应该是小写字符，并且除了 0 本身之外，答案中不应该有任何前置零。

注意: 不允许使用任何由库提供的将数字直接转换或格式化为十六进制的方法来解决这个问题。


```
impl Solution {
    /// 核心思路：
    /// 使用 u32 的补码表示，每次取最低 4 位（num & 0xF）查表得到对应十六进制字符，
    /// 然后右移 4 位，直到 num 为 0。
    ///
    /// 由于结果是从低位到高位生成的，最后反转即可得到正确顺序。
    /// 负数转 u32 后即为其补码形式，天然符合题目要求。
    pub fn to_hex(num: i32) -> String {
        if num == 0 {
            return "0".to_string();
        }

        const HEX: &[u8; 16] = b"0123456789abcdef";
        let mut num = num as u32;
        let mut bytes = Vec::with_capacity(8);

        while num != 0 {
            bytes.push(HEX[(num & 0xf) as usize]);
            num >>= 4;
        }

        // 从低位到高位生成，反转后即为正确顺序
        bytes.reverse();
        // SAFETY: 所有字节均来自 ASCII 常量 HEX，必为合法 UTF-8
        unsafe { String::from_utf8_unchecked(bytes) }
    }
}
```
