---
title: "leetcode-前缀和65"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 字母移位 II

给你一个小写英文字母组成的字符串 s 和一个二维整数数组 shifts ，其中 shifts[i] = [starti, endi, directioni] 。对于每个 i ，将 s 中从下标 starti 到下标 endi （两者都包含）所有字符都进行移位运算，如果 directioni = 1 将字符向后移位，如果 directioni = 0 将字符向前移位。

将一个字符 向后 移位的意思是将这个字符用字母表中 下一个 字母替换（字母表视为环绕的，所以 'z' 变成 'a'）。类似的，将一个字符 向前 移位的意思是将这个字符用字母表中 前一个 字母替换（字母表是环绕的，所以 'a' 变成 'z' ）。

请你返回对 s 进行所有移位操作以后得到的最终字符串。


```
impl Solution {
    pub fn shifting_letters(s: String, shifts: Vec<Vec<i32>>) -> String {
        let bytes = s.as_bytes();
        let n = bytes.len();

        // 差分数组：diff[i] 表示从位置 i 开始的累积偏移量变化
        let mut diff = vec![0; n + 1];

        // 构建差分数组
        for shift in &shifts {
            let start = shift[0] as usize;
            let end = shift[1] as usize;
            // direction=1 向后移 => +1, direction=0 向前移 => -1
            let delta = if shift[2] == 1 { 1 } else { -1 };

            diff[start] += delta;
            diff[end + 1] -= delta;
        }

        // 计算前缀和得到每个位置的最终偏移量
        let mut result = String::with_capacity(n);
        let mut current_shift = 0;

        for i in 0..n {
            current_shift = (current_shift + diff[i]) % 26;
            // 确保偏移量为正数（Rust 中 % 可能为负数）
            let shift_amount = (current_shift + 26) % 26;

            // 应用偏移：原始字符 + 偏移量，循环在 a-z 范围内
            let original = bytes[i] - b'a';
            let shifted = (original as i32 + shift_amount) % 26;
            let new_char = (shifted as u8 + b'a') as char;

            result.push(new_char);
        }

        result
    }
}
```
