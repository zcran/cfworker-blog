---
title: "leetcode-位运算12"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## UTF-8 编码验证

给定一个表示数据的整数数组 data ，返回它是否为有效的 UTF-8 编码。

UTF-8 中的一个字符可能的长度为 1 到 4 字节，遵循以下的规则：

1. 对于 1 字节 的字符，字节的第一位设为 0 ，后面 7 位为这个符号的 unicode 码。
2. 对于 n 字节 的字符 (n > 1)，第一个字节的前 n 位都设为1，第 n+1 位设为 0 ，后面字节的前两位一律设为 10 。剩下的没有提及的二进制位，全部为这个符号的 unicode 码。

这是 UTF-8 编码的工作方式：

      Number of Bytes  |        UTF-8 octet sequence
                       |              (binary)
   --------------------+---------------------------------------------
            1          | 0xxxxxxx
            2          | 110xxxxx 10xxxxxx
            3          | 1110xxxx 10xxxxxx 10xxxxxx
            4          | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
x 表示二进制形式的一位，可以是 0 或 1。

注意：输入是整数数组。只有每个整数的 最低 8 个有效位 用来存储数据。这意味着每个整数只表示 1 字节的数据。


```
impl Solution {
    /// 核心思路：
    /// 逐字节扫描，用字节前导 1 的个数判断当前字符的字节长度：
    /// - leading_ones == 0  → 单字节字符（0xxxxxxx）
    /// - leading_ones == 2/3/4 → 多字节字符，需要后续 (len-1) 个字节均为 10xxxxxx
    /// - 其他（1、5~8）→ 非法，直接返回 false
    ///
    /// 注意：题目保证每个整数只取最低 8 位，因此可安全转成 u8。
    pub fn valid_utf8(data: Vec<i32>) -> bool {
        let bytes: Vec<u8> = data.iter().map(|&x| x as u8).collect();
        let mut i = 0;

        while i < bytes.len() {
            match bytes[i].leading_ones() {
                // 单字节字符
                0 => i += 1,
                // 多字节字符的首字节
                len @ 2..=4 => {
                    let len = len as usize;
                    // 剩余字节不足以容纳该字符
                    if i + len > bytes.len() {
                        return false;
                    }
                    // 后续字节必须均为 10xxxxxx
                    if bytes[i + 1..i + len].iter().any(|&b| b.leading_ones() != 1) {
                        return false;
                    }
                    i += len;
                }
                // 非法首字节
                _ => return false,
            }
        }

        true
    }
}
```
