---
title: "leetcode-枚举45"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计对称整数的数目

给你两个正整数 low 和 high 。

对于一个由 2 * n 位数字组成的整数 x ，如果其前 n 位数字之和与后 n 位数字之和相等，则认为这个数字是一个对称整数。

返回在 [low, high] 范围内的 对称整数的数目 。


```
impl Solution {
    pub fn count_symmetric_integers(low: i32, high: i32) -> i32 {
        let mut res = 0;
        for num in low..=high {
            if num < 100 {
                // 两位对称数必须是 11 的倍数：11, 22, ..., 99
                if num % 11 == 0 {
                    res += 1;
                }
            } else if (1000..10000).contains(&num) {
                // 四位对称数：前两位数字之和 == 后两位数字之和
                let left = num / 1000 + (num / 100) % 10;
                let right = (num / 10) % 10 + num % 10;
                if left == right {
                    res += 1;
                }
            }
            // 其他位数（如三位、五位等）均不满足“2*n位”的要求，直接忽略
        }
        res
    }
}
```
