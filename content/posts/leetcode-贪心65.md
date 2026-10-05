---
title: "leetcode-贪心65"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 修改后的最大二进制字符串

给你一个二进制字符串 binary ，它仅有 0 或者 1 组成。你可以使用下面的操作任意次对它进行修改：

· 操作 1 ：如果二进制串包含子字符串 "00" ，你可以用 "10" 将其替换。
    · 比方说， "00010" -> "10010"
· 操作 2 ：如果二进制串包含子字符串 "10" ，你可以用 "01" 将其替换。
    · 比方说， "00010" -> "00001"

请你返回执行上述操作任意次以后能得到的 最大二进制字符串 。如果二进制字符串 x 对应的十进制数字大于二进制字符串 y 对应的十进制数字，那么我们称二进制字符串 x 大于二进制字符串 y 。


```
impl Solution {
    pub fn maximum_binary_string(binary: String) -> String {
        let n = binary.len();
        let bytes = binary.as_bytes();

        // 统计 '0' 的个数，并记录第一个 '0' 的位置
        let mut zeros = 0;
        let mut first_zero = 0;
        for (i, &b) in bytes.iter().enumerate() {
            if b == b'0' {
                if zeros == 0 {
                    first_zero = i;
                }
                zeros += 1;
            }
        }

        // 0 或 1 个 '0' 时已经是最大，无需操作
        if zeros <= 1 {
            return binary;
        }

        // 最终最大串只含一个 '0'，位置为 first_zero + zeros - 1
        let zero_pos = first_zero + zeros - 1;
        let mut result = vec![b'1'; n];
        result[zero_pos] = b'0';
        String::from_utf8(result).unwrap()
    }
}
```
