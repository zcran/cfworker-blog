---
title: "leetcode-计数66"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 判断一个数的数字计数是否等于数位的值

给你一个下标从 0 开始长度为 n 的字符串 num ，它只包含数字。

如果对于 每个 0 <= i < n 的下标 i ，都满足数位 i 在 num 中出现了 num[i]次，那么请你返回 true ，否则返回 false 。




```
impl Solution {
    pub fn digit_count(num: String) -> bool {
        let bytes = num.as_bytes();
        let n = bytes.len();

        // 统计每个数字的出现次数
        let mut count = [0; 10];
        for &b in bytes {
            count[(b - b'0') as usize] += 1;
        }

        // 检查每个位置 i 是否满足：数字 i 出现了 num[i] 次
        for i in 0..n {
            let expected = (bytes[i] - b'0') as usize;
            if count[i] != expected {
                return false;
            }
        }

        true
    }
}
```
