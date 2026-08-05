---
title: "leetcode-枚举28"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 移除指定数字得到的最大结果

给你一个表示某个正整数的字符串 number 和一个字符 digit 。

从 number 中 恰好 移除 一个 等于 digit 的字符后，找出并返回按 十进制 表示 最大 的结果字符串。生成的测试用例满足 digit 在 number 中出现至少一次。


```
impl Solution {
    pub fn remove_digit(number: String, digit: char) -> String {
        let digit = digit as u8;
        let bytes = number.as_bytes();
        let n = bytes.len();

        // 找到要移除的位置：移除后字典序最大的策略
        // 1. 如果 digit 后面有比它大的字符，移除这个 digit 会使后面的字符前移，
        //    从而得到更大的数（例如 "1231" 移除 '1'，第一个 '1' 后是 '2' > '1'）
        // 2. 否则移除最后出现的 digit（例如 "1321"，移除最后一个 '1'）
        let mut remove_pos = n - 1; // 默认移除最后一个
        for i in 0..n {
            if bytes[i] == digit {
                // 如果当前 digit 不是最后一个，且后一个字符比 digit 大
                // 移除当前 digit 能使更大的字符前移
                if i + 1 < n && bytes[i + 1] > digit {
                    remove_pos = i;
                    break;
                }
                // 记录最后一个 digit 的位置
                remove_pos = i;
            }
        }

        // 构建结果：跳过移除位置
        let mut result = String::with_capacity(n - 1);
        result.push_str(&number[..remove_pos]);
        result.push_str(&number[remove_pos + 1..]);
        result
    }
}
```
