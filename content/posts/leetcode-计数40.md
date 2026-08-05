---
title: "leetcode-计数40"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 所有子字符串美丽值之和

一个字符串的 美丽值 定义为：出现频率最高字符与出现频率最低字符的出现次数之差。

比方说，"abaacc" 的美丽值为 3 - 1 = 2 。

给你一个字符串 s ，请你返回它所有子字符串的 美丽值 之和。


```
impl Solution {
    pub fn beauty_sum(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let mut total = 0;

        // 枚举所有子字符串的起始位置
        for i in 0..n {
            let mut count = [0; 26];
            let mut max_freq = 0;

            // 从 i 开始扩展子字符串的结束位置
            for j in i..n {
                let idx = (s[j] - b'a') as usize;
                count[idx] += 1;
                if count[idx] > max_freq {
                    max_freq = count[idx];
                }

                // 找到最小频率（跳过出现次数为0的字符）
                let mut min_freq = i32::MAX;
                for &c in &count {
                    if c > 0 && c < min_freq {
                        min_freq = c;
                    }
                }

                total += max_freq - min_freq;
            }
        }

        total
    }
}
```
