---
title: "leetcode-贪心76"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 插入后的最大值

给你一个非常大的整数 n 和一个整数数字 x ，大整数 n 用一个字符串表示。n 中每一位数字和数字 x 都处于闭区间 [1, 9] 中，且 n 可能表示一个 负数 。

你打算通过在 n 的十进制表示的任意位置插入 x 来 最大化 n 的 数值 ​​​​​​。但 不能 在负号的左边插入 x 。

例如，如果 n = 73 且 x = 6 ，那么最佳方案是将 6 插入 7 和 3 之间，使 n = 763 。

如果 n = -55 且 x = 2 ，那么最佳方案是将 2 插在第一个 5 之前，使 n = -255 。

返回插入操作后，用字符串表示的 n 的最大值。


```
impl Solution {
    pub fn max_value(n: String, x: i32) -> String {
        let digit_byte = b'0' + x as u8;
        let bytes = n.as_bytes();

        // 正数：在第一个小于 x 的数字前插入；负数：在第一个大于 x 的数字前插入
        let negative = bytes[0] == b'-';
        let start = if negative { 1 } else { 0 };

        for i in start..bytes.len() {
            let b = bytes[i];
            let should_insert = if negative {
                b > digit_byte
            } else {
                b < digit_byte
            };
            if should_insert {
                let mut result = n;
                result.insert(i, digit_byte as char);
                return result;
            }
        }

        // 未找到插入点，追加到末尾
        let mut result = n;
        result.push(digit_byte as char);
        result
    }
}
```
