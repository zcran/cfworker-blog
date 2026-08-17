---
title: "leetcode-计数118"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 出现频率最低的数字

给你一个整数 n，找出在其十进制表示中出现频率 最低 的数字。如果多个数字的出现频率相同，则选择 最小 的那个数字。

以整数形式返回所选的数字。

数字 x 的出现频率是指它在 n 的十进制表示中的出现次数。


```
impl Solution {
    /// 返回 n 的十进制表示中出现频率最低的数字。
    /// 频率相同时返回数值更小的那个。若 n = 0，返回 0。
    pub fn get_least_frequent_digit(mut n: i32) -> i32 {
        if n == 0 {
            return 0;
        }

        let mut freq = [0u32; 10];
        while n > 0 {
            freq[(n % 10) as usize] += 1;
            n /= 10;
        }

        let mut best = 0;
        let mut min_cnt = u32::MAX;
        for (d, &c) in freq.iter().enumerate() {
            if c > 0 && c < min_cnt {
                min_cnt = c;
                best = d as i32;
            }
        }

        best
    }
}
```
