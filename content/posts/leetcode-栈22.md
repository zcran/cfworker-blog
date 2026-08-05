---
title: "leetcode-栈22"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 索引处的解码字符串

给定一个编码字符串 s 。请你找出 解码字符串 并将其写入磁带。解码时，从编码字符串中 每次读取一个字符 ，并采取以下步骤：

如果所读的字符是字母，则将该字母写在磁带上。

如果所读的字符是数字（例如 d），则整个当前磁带总共会被重复写 d-1 次。

现在，对于给定的编码字符串 s 和索引 k，查找并返回解码字符串中的第 k 个字母。


```
// 解码字符串索引查询
// 从后向前定位，避免实际解码
impl Solution {
    pub fn decode_at_index(s: String, mut k: i32) -> String {
        let chars: Vec<char> = s.chars().collect();
        let mut total_len = 0i64;
        let mut k = k as i64; // 转换为 i64 便于运算

        // 第一遍：计算解码后的总长度
        for &ch in &chars {
            if ch.is_ascii_digit() {
                total_len *= (ch as u8 - b'0') as i64;
            } else {
                total_len += 1;
            }
        }

        // 第二遍：从后向前定位第 k 个字符
        for &ch in chars.iter().rev() {
            if ch.is_ascii_digit() {
                let digit = (ch as u8 - b'0') as i64;
                total_len /= digit; // 回退到重复前的长度
                k %= total_len; // 将 k 映射到重复前的范围
                if k == 0 {
                    k = total_len; // 处理恰好整除的情况
                }
            } else {
                // 检查当前字符是否就是目标
                if k == total_len {
                    return ch.to_string();
                }
                total_len -= 1; // 回退一个字符
            }
        }

        unreachable!()
    }
}
```
