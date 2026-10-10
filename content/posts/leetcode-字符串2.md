---
title: "leetcode-字符串2"
date: 2026-10-10T12:56:51+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 字符串转换整数 (atoi)

请你来实现一个 myAtoi(string s) 函数，使其能将字符串转换成一个 32 位有符号整数。

函数 myAtoi(string s) 的算法如下：

1. 空格：读入字符串并丢弃无用的前导空格（" "）
2. 符号：检查下一个字符（假设还未到字符末尾）为 '-' 还是 '+'。如果两者都不存在，则假定结果为正。
3. 转换：通过跳过前置零来读取该整数，直到遇到非数字字符或到达字符串的结尾。如果没有读取数字，则结果为0。
4. 舍入：如果整数数超过 32 位有符号整数范围 [−2^31,  2^31 − 1] ，需要截断这个整数，使其保持在这个范围内。具体来说，小于 −2^31 的整数应该被舍入为 −2^31 ，大于 2^31 − 1 的整数应该被舍入为 2^31 − 1 。

返回整数作为最终结果。


```
impl Solution {
    pub fn my_atoi(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();
        let mut i = 0;

        // 1. 跳过前导空格
        while i < n && bytes[i] == b' ' {
            i += 1;
        }

        // 2. 处理符号
        let sign = if i < n && bytes[i] == b'-' {
            i += 1;
            -1i64
        } else {
            if i < n && bytes[i] == b'+' {
                i += 1;
            }
            1i64
        };

        // 3. 读取数字（使用 i64 累积，避免中途溢出）
        let mut num = 0i64;
        while i < n && bytes[i].is_ascii_digit() {
            num = num * 10 + (bytes[i] - b'0') as i64;
            // 提前截断，防止 i64 溢出（实际输入不会到这么长，但保险）
            if num > i32::MAX as i64 + 1 {
                return if sign > 0 { i32::MAX } else { i32::MIN };
            }
            i += 1;
        }

        // 4. 应用符号并截断到 i32 范围
        let num = sign * num;
        if num > i32::MAX as i64 {
            i32::MAX
        } else if num < i32::MIN as i64 {
            i32::MIN
        } else {
            num as i32
        }
    }
}
```
