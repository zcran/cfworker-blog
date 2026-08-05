---
title: "leetcode-枚举34"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 有效时间的数目

给你一个长度为 5 的字符串 time ，表示一个电子时钟当前的时间，格式为 "hh:mm" 。最早 可能的时间是 "00:00" ，最晚 可能的时间是 "23:59" 。

在字符串 time 中，被字符 ? 替换掉的数位是 未知的 ，被替换的数字可能是 0 到 9 中的任何一个。

请你返回一个整数 answer ，将每一个 ? 都用 0 到 9 中一个数字替换后，可以得到的有效时间的数目。


```
impl Solution {
    pub fn count_time(time: String) -> i32 {
        let bytes = time.as_bytes();
        let mut count = 1;

        // 处理小时部分 (位置 0 和 1)
        match (bytes[0], bytes[1]) {
            (b'?', b'?') => count *= 24,           // 00-23
            (b'?', b'4'..=b'9') => count *= 2,     // 0? 或 1?：只能 0 或 1
            (b'?', _) => count *= 3,               // 0?,1?,2?：0-2
            (b'0'..=b'1', b'?') => count *= 10,    // 0? 或 1?：00-19
            (b'2', b'?') => count *= 4,            // 2?：20-23
            _ => {} // 小时已确定
        }

        // 处理分钟部分 (位置 3 和 4)
        if bytes[3] == b'?' {
            count *= 6; // 十位：0-5
        }
        if bytes[4] == b'?' {
            count *= 10; // 个位：0-9
        }

        count
    }
}
```
